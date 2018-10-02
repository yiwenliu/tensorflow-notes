.. _scope:

Scope Mechanism
================
tf中所有socpe的概念都是为了在其范围内定义一个统一的参数，使代码更加简洁。

name scope
-----------
:ref:`Name scopes add a prefix to the name of all operations created in the same context. <ops-name>`, "the prefix"就是那个统一的参数

variable scope
----------------
:ref:`Variable scopes add a prefix to the name of all Variables created in the same context. <variable-name>`

arg scope
--------------
:ref:`define arguments for a list of operations <arg-scope>`