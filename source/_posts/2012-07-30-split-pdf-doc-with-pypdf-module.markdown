---
comments: true
date: 2012-07-30 22:43:51
layout: post
slug: split-pdf-doc-with-pypdf-module
title: 使用pyPdf切割pdf文档
wordpress_id: 2618
categories:
- gfrog-say[青蛙的杂烩]
---

青蛙拿到一份扫描版的pdf文档，每个pdf页包含了两页实际书籍页面，这种文档放在6寸Kindle上基本就是杯具了。于是青蛙试图找工具把他们切割掉。根据[铄日博客](http://note1day.com/)的推荐试验了[paper4kindle](http://note1day.com/2012/01/paper4kindle/)和[k2pdfopt](http://note1day.com/2011/12/k2pdfopt/)，但是papaer4kindle切割出来的文档页面错乱了，而且也没有修正pdf歪斜的功能，剪裁输出的文档很难看。k2pdfopt就更没法看了，从image识别文档的技术先进，但是对于每页pdf包含2页实际页面的文档，k2pdfopt基本无能为力。

青蛙本想探索一下paper4kindle输出之后页面错乱的问题，结果这玩意竟然不开源，又看到作者说花了几小时就搞出来了，那青蛙也看看pypdf包，试试能不能直接写代码自己搞定。

花了半下午时间看了下pypdf（ http://pybrary.net/pyPdf/ ）的API，然后直接把例子代码复制下来改吧改吧，咱也基本实现了paper4kindle的核心功能了，成功把那份文档处理掉了。至于那些花哨的界面啥的，有空再玩吧。代码先贴在这里，没有整理，暂时先不贴到github了，准备下一步尝试添加旋转页面的功能。

编码的时候发现了两个问题，



	
  * 设置mediaBox的位置之前需要先copy一份page对象，否则设置right的mediaBox的时候竟然会影响left的值，猜测是pypdf模块内部直接返回了page对象的引用，并没有复制一份返回。

	
  * 这份文档无法用paper4kindle编辑的原因是实际上文档的四个角坐标是反的，大概文档扫描之后被顺时针旋转了90度。



最后小小鄙视一下用python、用开源库写代码竟然不继续开源的某些软件作者。


    
    
    #!/usr/bin/env python
    
    import copy
    from pyPdf import PdfFileWriter, PdfFileReader
    
    output = PdfFileWriter()
    input1 = PdfFileReader(file("/tmp/a.pdf", "rb"))
    outputStream = file("/tmp/output.pdf", "wb")
    
    # print the title of document1.pdf
    print "title = %s" % (input1.getDocumentInfo().title)
    
    pages = map(input1.getPage, range(0, input1.getNumPages()))
    #pages = map(input1.getPage, range(0, 10))
    try:
        for page in pages:
            left = copy.copy(page)
            right = copy.copy(page)
    
            orig_upperLeft = page.mediaBox.getUpperLeft()
            orig_upperRight = page.mediaBox.getUpperRight()
            orig_lowerLeft = page.mediaBox.getLowerLeft()
            orig_lowerRight = page.mediaBox.getLowerRight()
    
            left.mediaBox.setUpperLeft((orig_upperLeft[0], orig_upperLeft[1]))
            left.mediaBox.setUpperRight((orig_upperRight[0], orig_upperRight[1] / 2))
            left.mediaBox.setLowerLeft((orig_lowerLeft[0], orig_lowerLeft[1]))
            left.mediaBox.setLowerRight((orig_lowerRight[0], orig_lowerRight[1] / 2))
            output.addPage(left)
    
            right.mediaBox.setUpperLeft((orig_upperLeft[0], orig_upperLeft[1]))
            right.mediaBox.setUpperRight((orig_upperRight[0], orig_upperRight[1]))
            right.mediaBox.setLowerLeft((orig_lowerLeft[0], orig_upperLeft[1] / 2))
            right.mediaBox.setLowerRight((orig_lowerRight[0], orig_upperRight[1] / 2))
            output.addPage(right)
    finally:
        # finally, write "output" to document-output.pdf
        output.write(outputStream)
        outputStream.close()
    
    print "new pdf has %s pages." % output.getNumPages()
    



参考文档：



	
  1. PYPDF: [http://pybrary.net/pyPdf/](http://pybrary.net/pyPdf/)

	
  2. The PDF file format: [http://www.prepressure.com/pdf/basics/fileformat](http://www.prepressure.com/pdf/basics/fileformat)

	
  3. Why my code not correctly split every page in a scanned pdf? [http://stackoverflow.com/questions/7047656/why-my-code-not-correctly-split-every-page-in-a-scanned-pdf](http://www.prepressure.com/pdf/basics/fileformat)


