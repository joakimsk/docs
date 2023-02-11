 
Customizing filter scripts
==========================

Polaric Server can configured with a set of filters. Filters may (1) do automatic tagging of points or (2) define what items are displayed on the map and how. Filters are defined in `/etc/polaric-aprsd/view.profiles` and this file is automatically compiled when the aprsd starts up. Errors in script are reported in the log file. It is also possible to check that the filter-script file is correct by running the following command in the shell::

    polaric-checkfilter

This script consists of a set of ``PROFILE`` declarations and (optionally) a special ``AUTOTAG`` profile. Each profile can be “exported” to clients and vi may specify what groups of users that have access to them 

Definitions
-----------

At any time in the script (but not within the scope of a profile or autotag definition), predicates may be assigned to identifiers. Se predicates below::

    ident = predicate ;

Rules
-----

A rule consists of a predicate and an action. If the predicate evaluates to true, the action is applied.

Predicates
----------

Predicates are boolean expressions that are evaluated with respect to to each point item to be displayed on map. A simple predicate 
expression can be one of the following: 

+-------------------------------+---------------------------------------------------------------------------------+
| *ident*                       | Where ident is defined in the script to be a predicate                          |
+-------------------------------+---------------------------------------------------------------------------------+
| \* *tag*                      | True if tag is set                                                              |
+-------------------------------+---------------------------------------------------------------------------------+
| ``infra``                     | True if item is ative infrastructure                                            |
+-------------------------------+---------------------------------------------------------------------------------+
| ``fulldigi``                  | True if item is a full digipeater                                               |
+-------------------------------+---------------------------------------------------------------------------------+
| ``moving``                    | True if item is moving (changing)                                               |
+-------------------------------+---------------------------------------------------------------------------------+
| ``igate``                     | True if item is an igate                                                        |
+-------------------------------+---------------------------------------------------------------------------------+
| *ident* *relop* *num*         | Test the numeric value of ident [#]_                                            | 
+-------------------------------+---------------------------------------------------------------------------------+
| *ident* ~ “*regexp*”          | True if value of ident matches the regexp (regular expression) [#]_             |
+-------------------------------+---------------------------------------------------------------------------------+

.. [#] *relop* is a relational operator ( =, <, >, ⇐ or >= ). *num* is an integer. *ident* can be ``scale`` (current 
       scale of the map in client browser), ``max-speed`` (the highest speed reported for the item during the length of its trail), or 
       ``average-speed`` (the average speed for an item)

.. [#] *ident* can be a '*symbol*' (the two characters of an APRS symbol, symbol table/overlay first), '*ident*' (APRS callsign), 
       '*path*' (digipeater path) or '*source*' (name of the source, typically TNC or APRS/IS channel) of the item in question. 

Simple predicates can be combined into complex expressions using ``AND``, ``OR``, ``NOT`` operators or parantheses. Recursive grammar like this (left associativity and precedence to ``NOT`` before ``AND``/``OR`` and ``AND`` before ``OR``)::

    <predicate> ::= <predicate> AND <predicate>
                |  <predicate> OR <predicate>
                |  NOT <predicate>
                |  '(' <predicate> ')'
              
Logical operators can be written in lowercase as well as uppercase letters as well as with symbols '|' or '&'. 
                

Actions
-------

An action is enclosed in curly parantheses, and defines if and how the item in question should be shown on the map or (if the rule is a ``AUTOTAG`` rule) what tags that are to be set on the item. For view-filters It can be one or more of the following (separated by comma): 

========================  ============================================
``hide-all``              Don't show the item on the map at all.
``hide-ident``            Don't show the identifier for the item on the map
``hide-trail``            Don't show a moving item 's trail on the map.
``hide-alias``	          Don't show alias
``trail-time`` = *num*    Max time [#]_ with inactivity before trail is removed from map.
``trail-length`` = *num*  Trail length [#]_
``show-path``	          Show digipeating path graphically [#]_
``style`` = “…”	          Adds one or more CSS classes to item (separated with whitespace).
``icon`` = “…”	          Sets the icon (filename). 
========================  ============================================

.. [#] In minutes

.. [#] In minutes

.. [#] If item is active infrastructure (digipeater), show how APRS packets have been routed through 
       it (draw lines on the map between items).
       
Note that if multiple actions are performed their results are added to each other. If style is applied multiple times, CSS class strings are simply concatenated. Note that order may be significant because of the way cascading of stylesheets works. Icons or trail-times are just overridden (replaced).  

**Example**::

 rule1 => { hide-ident, style="myclass", trail-length=60 };
 rule2 => { show-path, style="yourclass", trail-length=70 };

If both of these two rules matches an item, the resulting action is equivalent to saying::

 rule3 => { hide-ident, show-path, style="myclass yourclass", trail-length=70 };


Auto Tagging
------------

A tag is a keyword that can be used in searching or in filter scripts. Tags are set either by the user, by the system or by scripts by defining rules inside an ``AUTOTAG`` block. These rules are evaluated when new points are added or existing points are changed. Note that autotagging rules can only be used to add tags, not to remove existing tags. So, if an autotag rule has set a tag and the predicate later evaluates to false, the tag will still be there. This limitation may be addressed in future work::

 AUTOTAG {
    [ <predicate> => <tags>; ] *
 }

<tags> is a comma separated list of keywords starting with '\*' between curly braces. For example to mark all AIS vessels with tags AIS.medical and APRS points with the ambulance or ambulance-boat symbol with tag 'ambulance', the rule can be::

 AUTOTAG {
     *AIS.medical OR symbol ~ "/a" OR symbol ~ "Es" => { *ambulance };
 }
 

View Profiles
-------------

A profile is identified by a name. It consists of zero or more of rules each with a predicate and an action. The rule set is applied to each item. If predicate evaluates to true, the associated action is performed. If a profile perform multiple actions for an item, their results are combined (see below).

We may also include the rules from other profiles. An include clause can appear anywhere in the list of rules, but normally, ``INCLUDE``'s should come before rules::

 PROFILE <ident> {
     [ EXPORT '"'<description>'"' => PUBLIC | '{' <group-list> '}' ; ]
     [  <predicate> => <action>; | INCLUDE <profile-name>; ] *
 }

A export clause declares that the profile should be visible in the menu of filters shown on clients. ``PUBLIC`` means that the profile is visible for all, alertnatively we may specify a list of groups that have access to the profile. We may also use the keyword ``NOLOGIN`` as a special group, i.e. those who are not logged in to the system.

**Example:**

In the example below prof2 includes the rules of prof1 before adding its own rules. Prof2 is also accessible and visible in the filter-menu for members of mygroup and users that are not logged in::

 PROFILE prof1 {
     ...
 }

 PROFILE prof2 {
    EXPORT "Profile number two" => { NOLOGIN, mygroup };
    INCLUDE prof1;
    ...
 }


Comments
--------

Any lines starting with '#' are considered comments and ignored.

