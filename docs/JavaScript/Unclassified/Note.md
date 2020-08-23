---
layout: default
title: "Unclassified | Note"
description: "This is a note."
---

## Download File

> [@StackOverflow](https://stackoverflow.com/questions/25826573/download-txt-using-javascript-without-dialog-prompt)

> To download a file in javascript locally without prompting a dialog box, be sure to enable it in your browser settings (chrome >> settings >> advanced >> downloads and turn off 'Ask where to save each file before downloading'.
>    
> Subsequently, you can write a simple text file like so using blob objects:

```js
function save() {
    var content = ["your-content-here"];
    var bl = new Blob(content, {type: "text/plain"});
    var a = document.createElement("a");
    a.href = URL.createObjectURL(bl);
    a.download = "your-download-name-here.txt";
    a.hidden = true;
    a.click();
}
```