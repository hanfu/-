********
关于写作
********

RST常用语法
=======

各种字体
	*斜体*

	**粗体**

	``小红字``

	:class:`小黑粗`

	:guilabel:`小篮筐`

	.. centered:: 中间

	::

		if 代码框:
			more

	:math:`X_{0:5} = 公式(X_0)`


	.. admonition:: admonition框

	   蓝色的框

	.. Note:: blue

	.. Attention:: orange

	.. Caution:: orange 

	.. Warning:: orange

	.. seealso:: yellow

	.. seealso:: Wave shapes are implemented in :f:func:`morphevolution/RvR`
             and :f:func:`morphevolution/vT`.

	.. DANGER:: red

	.. Error:: red

	.. Hint:: green

	.. Important:: green

	.. Tip:: green







各种连接
	`连接 <https://hanfu.us>`_

	.. _anchor: [external.url.if.any]

	use anchor_

	footnote [#this]_

	.. [#this] this is a footnote

	.. math::
		:label: this_eq

		X_{0:5} = (X_0)

	引用公式 :eq:`this_eq`

	.. code-block:: python
		:caption: this_code
		:name: this_code

		if code-block:
			more

	引用代码 :ref:`this_code`


.. sidebar:: 一个框

	一些文字

文字框
	另一些文字

图片
	.. image:: path/to.jpg
		:target: somelink_
		:alt: alt-image
		:align: center
		:width: 400px



