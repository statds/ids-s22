# Markdown Basics

Markdown is an incredible tool for developing readable files for people to view. Using Markdown you can present your code and ideas in a format that is much easier to understand.

## Tables 
The table making process in markdown is interesting. You need to actually make a table with `|` this symbol and dash marks `-`
For example:


`| Column 1      | Column 2      | Column 3  |`<br>
`|---------------|---------------|-----------|`<br>
`| Row 1         |               |           |`<br>
`| Row 2         |               |           |`<br>
`| Row 3         |               |           |`<br>


This piece of code would look like this when run throw markdown:

| Column 1      | Column 2      | Column 3  |
|---------------|---------------|-----------|
| Row 1         |               |           |
| Row 2         |               |           |
| Row 3         |               |           |


***

# Now a quick rundown of all the boring things that people should know but aren't worth an entire talk on 

| What it is?|How You do it?|Example code|Shown example|
|:---------------:|:----------------:|:-----------:|:-------------:|
|Italics|Surround the words you want to use with a single asterisk|`*like this*` or `_this way_`| *like this* or _this way_ |
|Bold|Surround the words you want to use with two single asterisk|`**example text**` or `__other example text__`|**example text** or __other example text__|
|Bold AND Italic|Surround the words you want to use with three single asterisk|`***Last One***` or `___I promise___`|***Last One*** or ___I promise___|
|Strike-through|Surround text with two tiltes|`~~This is what it looks like~~`|~~This is what it looks like~~
|Links|Place the text you want in brackets next to the link you want in parenthesis<br><br>If you don't care about the title just put it in <>|`[What time is it.com](http://www.whattimeisit.com/) <https://www.google.com/>`|[What time is it.com](http://www.whattimeisit.com/)<br> <https://www.google.com/>|
|Images|same as a URL link but with an `!` before it|`![husky logo](https://uconn-today-universityofconn.netdna-ssl.com/wp-content/uploads/2013/04/husky_qa.jpg.jpeg)`|![husky logo](https://uconn-today-universityofconn.netdna-ssl.com/wp-content/uploads/2013/04/husky_qa.jpg.jpeg)|

Other things that need to be shared cannot go into the table so here are the rest of the basics

## Numbering
1. If you put a number in front of the point it numbers it
5. Here is an example of the text
5. `1. It's that easy`<br> `2. Just like this`
5. You can actually number them however you'd like

___

7. If you start with 7 it begins at 7
2. But from there on out
1. You can do
35. Whatever you want


`7. If you start with 7 it begins at 7`<br>
`2. But from there on out`<br>
`1. You can do`<br>
`35. Whatever you want`<br>


## Titles
Use `#` the pound sign. Each of the numbers correspondes to the number of pound signs

# 11111
## 22222
### 33333
#### 44444

## Bullet Points and other listing
- You just put a `-`, `*`, or `+` before the word making sure to give a space between them
- `- Just like that` or `* or use this` or `+ or this`
    - Also if you tab in you can put a bullet further in
        - You
            - Can
                - Keep
                    - Going
                        - Forever


## Block quotes
>This is a block quote. You make it by putting `>` this in front
>> Put two to make a nested block quote

You can then put info in between the breaks as long as there is a full line with nothing between them
>>Then hop right back in. I think this would help with citations or general notes
>>>You
>>>>Can
>>>>>Also
>>>>>>Do
>>>>>>>This
>>>>>>>>Forever

<br> <br>
Additionally, when wanting multiple lines you can't put two block quotes back to back. Let me show you. <br>
`>Hello`
<br>
`>Good Day`
<br>
Comes out as
>Hello
>Good Day

But if you do this<br>
`>You are great` <br>
`>` <br>
`>You deserve to be happy` <br>

It comes our correctly as
>You are great
>
>You deserve to be happy


## Highlighting Info
Placing a back dash around code `gives inline code` or can be used to highlight important information
___

<dl>
  <dd>This is the symbol  `</dd>
  <dd>Do not use the normal which looks like this -> ' You want this-> `</dd>

</dl>

## In-line code
Different Programming Languages can be used in markdown

### Any generic code
```
    {If you surroudning the code in three back dashes 
    You can do literally anything and it'll 
    display as you'd expect normally}
    [by adding the name of the language after the first set
    of back dashes you can have the words color as
    you'd expect in the language]
```

### Java
```javascript
var s = "This is java";
alert(s);
```

### Python
```python
s = "This is Python"
print s
```

## Task list
Personally, I love task list more than is probably reasonable for a person but they're the only way I can get things done<br>
Creating them in Markdown is easy. <br>
`- [x] Done`<br>
`- [ ] Not done`
- [x] Done
- [ ] Not done

## Additional fun tricks using HTML
### Collapsable Menus
<details>
<summary>CLICK ME</summary>
You can put things inside of dropdowns 
</details>

`<details>` <br>
`<summary>CLICK ME</summary>`<br>
`You can put things inside of dropdowns`<br>
`</details>`<br>

### Aligning images with the different sides of the sheet
This is the code you need to align images to the left:
<img align="left" width= "50" height="50" src="https://supplyroom.s3.amazonaws.com/F/FS-/FS-P/FS-PA-1_W.png"> <br>
`<img align="left" width= "50" height="50" src="https://supplyroom.s3.amazonaws.com/F/FS-/FS-P/FS-PA-1_W.png">`
<br>
<br>

___
This is the code you need to align images to the right:
<img align="right" width= "50" height="50" src="https://supplyroom.s3.amazonaws.com/F/FS-/FS-P/FS-PA-1_W.png"> <br>
`<img align="right" width= "50" height="50" src="https://supplyroom.s3.amazonaws.com/F/FS-/FS-P/FS-PA-1_W.png">`


If you do nothing it will automatically allign with the middle.

### Changing Font Color
To make <span style="color:blue">blue</span> or <span style="color:green">green</span> or <span style="color:red">red</span> text you just need to change that setting the code like this. <br>
`<span style="color:blue">blue text</span>.`


# Mathematical and Statistical operations
As we are in a stat class I think it is only right that I show how to make mathematical formulas

|The Name|the Code|The Image|
|:---------------:|:---------------:|:-----------:|
|Addition|`$+$`|$+$|
|Subtraction|`$-$`|$-$|
|Multiplication|`$*$`|$*$|
|Division|`$/$`|$/$|
|Equal|`$=$`|$=$|
|Fractions|`$\frac{n}{k}$`|$\frac{n}{k}$|
|Square|`$n^2$`|$n^2$|
|General Power|`$n^k$`|$n^k$|
|Square Root|`$\sqrt{k}$`|$\sqrt{k}$|
|General Root|`$\sqrt[n]{k}$`|$\sqrt[n]{k}$|
|Sine|`$\sin$`|$\sin$|
|Cosine|`$\cos$`|$\cos$|
|Tangent|`$\tan$`|$\tan$|
|Alpha|`$\alpha$`|$\alpha$|
|Beta|`$\beta$`|$\beta$|
|Gamma|`$\gamma$`|$\gamma$|
|Pi|`$\pi$`|$\pi$|
|Phi|`$\phi$`|$\phi$|
|Chi|`$\chi$`|$\chi$|
|Delta|`$\delta$`|$\delta$|
|Lambda|`$\lambda$`|$\lambda$|
|Theta|`$\theta$`|$\theta$|
|Limit|`$\lim$`|$\lim$|
|Expected|`$\exp$`|$\exp$|
|To|`$\to$`|$\to$|
|Infinity|`$\infty$`|$\infty$|
|Choose|`$\binom{n}{k}$`|$\binom{n}{k}$|
|Sigma Notation|`$\sum$`|$\sum$|
|Pi Notation|`$\prod$`|$\prod$|
|Prime|`$a’$`|$a’$|
|Integral|`$\int_0^\infty \mathrm{x},\mathrm{d}x$`|$\int_0^\infty \mathrm{x},\mathrm{d}x$|
|Hat|`$\hat{a}$`|$\hat{a}$|
|Bar|`$\bar{a}$`|$\bar{a}$|

# Other Fun Helpful notes

1. Triple *Underscore*, *Asterisk*, or _dash_ gives you a line to break up text
___
***
---
2. Adding a `<br>` in your code will force a line break <br>
    a. This can be very helpful for keeping lines condensed in a readable format<br>
    b. For example, in this code right here the sublist (a,b) function doesn't exist so I forced line breaks and added it

**Here's the code**
<br>
<br>
``2. Adding a <br> in your code will force a line break`` <br>
    `a. This can be very helpful for keeping lines condensed in a readable format`<br>
    `b. For example, in this code right herem the sublist (a,b) function doesn''t exist so I forced line break and added it`
    

**Without the line breaks it would look like this**

3. Adding a `<br>` in your code will force a line break
    a. This can be very helpful for keeping lines condensed in a readable format
    b. For example, in this code right here the sublist (a,b) function doesn't exist so I forced line break and added it
<br>
<br>
4. You can use HTML formatting to display things
    - I found this useful because some things cannot be displayed in Markdown because their functions prevent it
    - By using HTML you can type whatever you'd like and the functions of those symbols wont interfer
    - Displaying the symbol for highlighting things was very difficult until I did that
    

5. If you are using the back ticks to show information and would like to show the back ticks there is a trick for it<br>
- ``This line right here is in back ticks but `I can still` show the back ticks``
- This can be done by starting the string with a double set of back ticks and using a single back tick for the info inside the back ticks
- The line above looks like this <br>
-  ``This line right here is in back ticks but `I can still` show the back ticks``

6. In some situations you can display code or other text using a single tab for convenience <br>
    `Notice how you an see the back dash here````` That's because this line is tabbed in once'


7. You can also cancel any special character by putting a `\` before it
`\*I like volleyball\*` should display like **this** <br><br> I like volleyball <br><br> not like **this**<br><br> *I like volleyball*

Thank you!
