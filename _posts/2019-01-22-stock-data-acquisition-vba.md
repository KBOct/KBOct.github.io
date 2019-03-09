---
title: "Stock Data Acquisition using Excel VBA"
date: 2019-01-22
categories: [quantitative finance, data science, prediction, vba]
tags: [stock, data, live stock data, updated stock data, analysis, acquisition, excel, spreadsheet, vba]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Stock Data Acquisition using Excel VBA"
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---
In this post I will try to sum up the different ways in which one can acquire stock data to use in different frameworks and programming languages, essentially for resource allocation, portfolio optimization and rebalancing or to elaborate quantitative trading strategies.

Because of the changes that have been occuring following Yahoo's API shutdown I will focus on the source of the data.

#Yahoo!

## Data acquisition in VBA

Here we will get the PE Ratio, Forward PE, Beta, Market Cap and 52 Week High. A way to acquire such data in VBA would be to use this Subroutine :

{% highlight vb linenos %}
Sub qTest_3()

    Call clear_data

    Dim myrng As Range
    Dim lastrow As Long
    Dim row_count As Long
    Dim ws As Worksheet
    Set ws = Sheets("Main")

    col_count = 2
    row_count = 2

    'Find last row
    With ws
     lastrow = .Range("A" & .Rows.Count).End(xlUp).Row
    End With

    'set ticker range
    Set myrng = ws.Range(Cells(2, 1), Cells(lastrow, 1))

    'loop through tickers
    For Each ticker In myrng

        'Send web request
        Dim URL2 As String: URL2 = "https://finance.yahoo.com/quote/" & ticker & "/key-statistics?p=" & ticker
        Dim Http2 As New WinHttpRequest

        Http2.Open "GET", URL2, False
        Http2.Send

        Dim s As String
        'Get source code of site
        s = Http2.ResponseText

            Dim metrics As Variant
            '**** Metric fields here
            metrics = Array("trailingPE", "forwardPE", "beta", "marketCap", "fiftyTwoWeekHigh")


            'Split string here
            For Each element In metrics


                firstTerm = Chr(34) & element & Chr(34) & ":{" & Chr(34) & "raw" & Chr(34) & ":"
                secondTerm = "," & Chr(34) & "fmt" & Chr(34)

                nextPosition = 1

                On Error GoTo err_hdl

                Do Until nextPosition = 0
                    startPos = InStr(nextPosition, s, firstTerm, vbTextCompare)
                    stopPos = InStr(startPos, s, secondTerm, vbTextCompare)
                    split_string = Mid$(s, startPos + Len(firstTerm), stopPos - startPos - Len(secondTerm))
                    nextPosition = InStr(stopPos, s, firstTerm, vbTextCompare)

                    Exit Do
                Loop

                On Error GoTo 0


                Dim arr() As String
                arr = Split(split_string, ",")
                metric = arr(0)

                'Output to sheet
                ws.Range(Cells(row_count, col_count), Cells(row_count, col_count)).Value = metric
                col_count = col_count + 1

getData:

            Next element

            Dim symbol As String
            symbol = ticker

            col_count = 2
            row_count = row_count + 1

    Next ticker

    MsgBox ("Done")

    Exit Sub

err_hdl:
    ws.Range(Cells(row_count, col_count), Cells(row_count, col_count)).Value = "N/A"
    Resume getData

End Sub


Sub clear_data()

    Dim ws As Worksheet
    Set ws = Sheets("Main")
    Dim lastrow, lastcol As Long
    Dim myrng As Range

    With ws
     lastrow = .Range("A" & .Rows.Count).End(xlUp).Row
    End With

    lastcol = ws.Cells(1, Columns.Count).End(xlToLeft).Column

    Set myrng = ws.Range(Cells(2, 2), Cells(lastrow, lastcol))

    myrng.Clear

End Sub
{% endhighlight %}

## Data acquisition in Python


## Data acquisition in R

## Data acquisition in C++

# Google

## Morningstar

## Getting live stock data without code

# Quandl
<!-- # H1 Heading

## H2 Heading

### H3 Heading

Here's some basic text

And here's some *italic*

Here's some **bold** text

What about a [link](https://github.com/kboct)

Here's a bulleted list:
* First
+ Second
- Third


Here's a numbered list:
1. First
2. Second
3. Third -->



<!-- Here's some inline code `x+y`

Here's an image:
<img src="{{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/image1.jpg" alt="scilab numerical analysis plot" class="full">


Here's another image using Kramdown:
![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/image1.jpg)
{: .full}

Here's some math :

$$z=x+y$$

You can also put it inline $$z=x+y$$ -->
