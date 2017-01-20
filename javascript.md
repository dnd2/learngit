## DOM
1） 查找指定元素的前一个元素

~~~javascript
function prev( elem ) {
	do {
	  elem = elem.previousSibling;
	} while ( elem && elem.nodeType != Node.ELEMENT_NODE );
	
	return elem;
}
~~~
Note: The read-only **Node.nodeType** property that represents the type of the node.

The nodeType property can be used to distinguish different kind of nodes, such that <font color="#0095dd">elements</font>, <font color="#0095dd">text</font> and <font color="#0095dd">elements</font>, from each other.

#### Node type constants

Constant      	  | Value         | Description
-------------      | ------------- | -------------  
**Node.ELEMENT_NODE**  			  | 1| An Element node such as `<p>` or `<div>`.  |
**Node.TEXT_NODE**     			  | 3| The actual Text of Element or Attr.  |
**Node.COMMENT_NODE**  			  | 8| A Comment node.  |
**Node.DOCUMENT_NODE** 			  | 9| A Document node.A DocumentFragment node.  |
**Node.DOCUMENT_FRAGMENT_NODE** | 9| Content Cell  |

2）查找指定元素的下一个元素

~~~javascript
function next( elem ) {
	do {
		elem = elem.nextSibling;
	} while ( elem && elem.nodeType != Node.ELEMENT_NODE );
	
	return elem;
}
~~~

3）查找第一个元素

~~~javascript
funciton first( elem ) {
	elem = elem.firstChild;
	return elem && elem.nodeType != Node.ELEMENT_NODE ?
		next( elem ) : elem; 
}
~~~

4) 查找最后一个元素

~~~javascript
functoin last( elem ) {
	elem = elem.lastChild;
	return elem && elem.nodeType != Node.ELEMENT_NODE ? 
		prev( elem ) : elem; 
}
~~~

### 绑定到每个HTML元素
通过在HTMLElement的原型上添加方法，可以在dom元素上直接调用该方法。

~~~javascript
HTMLElement.prototype.next = function() {
	var elem = this; // 指向当前dom元素
	do {
		elem = elem.nextSibling;
	} while ( elem && elem.nodeType != Node.ELEMENT_NODE );
	
	return elem;
}

// So we can use it as follow:
document.body.first().next();
~~~

**HTMLElement**

The <font color="#0095dd">**HTMLElement**</font> interface represents any HTML element. Some elements directly implement this interface, others implement it via an interface that inherit it.

![HTMLElement Inheritance](http://images.cnblogs.com/cnblogs_com/longway/HTMLElementInIE.jpg)

在IE 6-8中不支持该接口对象, 修复代码：

~~~javascript
var DOMElement ={
    extend: function(name,fn){//添加名称为name的方法fn
    
        if(!document.all)//除了ie而外的浏览器都能够访问到HTMLElement这个类
            eval("HTMLElement.prototype." + name + " = fn");
        else{
            //    IE中不能访问HTMLElement这个类
            //    为了达到同样的目的，必须重写下面几个函数
            //    document.createElement
            //    document.getElementById
            //    document.getElementsByTagName
            //    这几个函数都是获得HTML元素的方法
            //    修改这些方法，使得通过这些方法获得的每个元素拥有名称为name的方法fn

            var _createElement = document.createElement;
            document.createElement = function(tag){
                var _elem = _createElement(tag);
                eval("_elem." + name + " = fn");//_elem[name] = fn;也可以达到同样的目的
                return _elem;
            }

            var _getElementById = document.getElementById;
            document.getElementById = function(id){
                var _elem = _getElementById(id);
                eval("_elem." + name + " = fn");
                return _elem;
            }

            var _getElementsByTagName = document.getElementsByTagName;
            document.getElementsByTagName = function(tag){
                var _arr = _getElementsByTagName(tag);
                for(var _elem=0;_elem<_arr.length;_elem++)
                    eval("_arr[_elem]." + name + " = fn");
                return _arr;
            }
        }
    }
};
//测试方法
DOMElement.extend("contents",function(){return this.innerHTML});
~~~

在实际使用中，在用childNodes的时候，FF下会有#text节点，每次都需要手动来过滤一次，通过上面介绍的方法来扩展HTMLElement可以很简单的写出一个公用的方法，以后使用就很方便了：

~~~javascript
DOMElement.extend("getSafeChildNodes",
function(){
    var tempNodes = [];
    for(var i=0;i<this.childNodes.length;i++){
        if(this.childNodes[i].nodeName=='#text')
            continue;
        tempNodes.push(this.childNodes[i]);
    }
    return tempNodes;
});
~~~

另外，HTML元素的getElementsByTagName（sTagName)会把这个元素之下所有tagName为sTagName的节点返回，无论是否是直接的子节点。但是在有些时候我们需要此节点直接的子节点并且满足tagName为sTagName，所以就扩展了一个方法getChildNodesByTagName：

~~~javascript
DOMElement.extend("getChildNodesByTagName",
function(tagName){
    var tempNodes = [];
    for(var i=0;i<this.childNodes.length;i++){
        if(this.childNodes[i].nodeName.toLowerCase() == tagName){
            tempNodes.push(this.childNodes[i]);
        }
    }
    return tempNodes;
});
~~~

测试这几个方法，有如下HTML代码：

~~~html
<body>
    <div id="div1">div1</div>
    <div id="div2">
        <div id="div3">
            <div id="div4"></div>
        </div>
        <div id="div5"></div>
        <div id="div6"></div>
    </div>
</body>
~~~

测试代码

~~~javascript
onload = function(){
    alert(document.getElementById("div1").contents());
    alert(document.getElementById("div2").getChildNodesByTagName("div").length);
    alert(document.getElementById("div2").getElementsByTagName("div").length);
    alert(document.getElementById("div2").getSafeChildNodes().length);
    alert(document.getElementById("div2").childNodes.length);
}
~~~

* 第一个方法会相当于innerHTML属性，返回的值为“div1”；
* 第二个方法会获得子节点中tagName为div的节点的长度，返回值为3（不包括div4）；
* 第三个方法是返回子节点（包括间接的子节点）中tagName为div的节点的长度，返回值为4（包括div4）；
* 第四个方法是返回子节点，在ff中过滤掉“#text”节点，在各种浏览器中都返回3；
* 第五种方法是js自带的子节点属性，在ff中返回7，在ie中返回3。

5) 根据HTML Tag定位元素

~~~javascript
function tag( name, elem ) {
	return ( elem || document ).getElementsByTagName( name );
}
~~~

6) 等待HTML DOM加载完成

HTML解析完成 --> 外部js和css加载完毕 --> 脚本在文档解析完成并执行 --> HTML DOM 完全构造起来 --> 图片和外部内容加载 --> 网页加载完毕

~~~javascript
   /**
	 * A function for watching the DOM until it's ready
	 */
	domReady: function( f ) {
		// If the DOM is already loaded, execute the function right away
		if ( domReady.done ) return f();
		// If we've already added a function
		if ( domReady.timer ) {
			// Add it to the list of functions to execute
			domReady.ready.push( f );
		} else {
			// Attach an event for when the page finishes loading,
			// just in case it finishes first. Uses addEvent.
			addEvent( window, "load", isDOMReady );
			// Initialize the array of functions to execute.
			domReady.ready = [f];
			// Check to see if the DOM is ready as quickly as possible
			domReady.timer = setInterval( isDOMReady, 13 );
		}	 
	},
	/**
	 * Checks to see if the DOM is ready for navigation
	 */
	isDOMReady: function() {
		// If we already figured out that the page is ready, ignore
		if ( domReady.done ) return false;

		// Check to see if a number of functions and elements are
		// able to be accessed
		if ( document && document.getElementsByTagName &&
			 document.getElementById && document.body ) {
			// If they're ready, we can stop checking 
			clearInterval( domReady.timer );
			domReady.timer = null;
			// Execute all the functions that were waiting
			for ( var i = 0; i < domReady.ready.length; i++ ) {
				domReady.ready[i]();
			}
			// Remember that we're now done
			domReady.ready = null;
			domReady.done  = true;
		}
	},
~~~ 

7）通过类查找元素/添加、删除、查找类

~~~javascript
function hasClass( name, type ) {
	var r  = [];
	var re = new RegExp( "(^|\\s)" + name + "(\\s|$)" );
	var e  = document.getElementsByTagName( type || "*" );
	
	for ( var j = 0; j < e.length; j++ ) {
		if ( re.test( e[j] ) )
			r.push( e[j] );
	} 
	
	return r;
}

// We can use
// Check the element whether constains a class name named as 'sClass'
function hasClass( element, sClass ) {
		if ( ! element.classList ) {
			return element.className.match( new RegExp( "(\\s|^)" + sClass + "(\\s|$)" ) );	
		} else {
			return element.classList.contains( sClass );
		}
}

// Add a new style
function addClass( element, newClassName ) {
		if ( ! element.classList ) {
			if ( ! this.hasClass( element, newClassName ) )	{
				element.className += " " + newClassName;
			}
		} else {
			element.classList.add( newClassName );	
		}
}

function removeClass( element, oldClassName ) {
		if ( ! element.classList ) {
			if ( this.hasClass( element, oldClassName ) ) {
				var reg = new RegExp( "(\\s|^)" + oldClassName + "(\\s|$)" );
				element.className = element.className.replace( reg, "" );
			}
		} else {
			element.classList.remove( oldClassName );
		}
}
~~~

Node: The **Element.classList** is a read-only property which returns a live <font color="#0095dd">DOMTokenList</font> collection of the class attributes of the element.

Using **classList** is a convenient alternative to accessing an element's list of classes as a space-delimited string via <font color="#0095dd">element.className</font>

8) 获取元素文本内容

~~~javascript
function text( e ) {
	var t = "";
	
	e = e.childNodes || e;
	
	for ( var j = 0; j < e.length; j++ ) {
		t += e[j].nodeType != Node.ELEMENT_NODE ?
			e[j].nodeValue : text( e[j].childNodes );
	}
	
	return t;
}
~~~

Note: 
<font color="#0095dd">**Node.nodeValue**</font> The Node.nodeValue property returns or sets the value of the current node.

For the document itself, nodeValue returns null. For text, comment, and CDATA nodes, nodeValue returns the content of the node. For attribute nodes, the value of the attribute is returned.

The <font color="#0095dd">**Node.childNodes**</font> read-only property returns a live collection(即时更新的集合) of child nodes of the given element.

The return value is a NodeList. It is an ordered collection of node objects that are children of the current element.

~~~javascript
// parg is an object reference to a <p> element

if (parg.hasChildNodes()) {
  // So, first we check if the object is not empty, if the object has child nodes
  var children = parg.childNodes;

  for (var i = 0; i < children.length; i++) {
    // do something with each child as children[i]
    // NOTE: List is live, Adding or removing children will change the list
  }
}

// This is one way to remove all children from a node
// box is an object reference to an element with children

while (box.firstChild) {
    //The list is LIVE so it will re-index each call
    box.removeChild(box.firstChild);
}
~~~

9) 检查元素属性

~~~javascript
function hasAttribute( elem, name ) {
	if ( elem.hasAttribute ) {
		return elem.hasAttribute( name );
	} 
	return elem.getAttribute( name ) != null;
}
~~~

<font color="#0095dd">**getAttribute()**</font> returns the value of a specified attribute on the element. If the given attribute does not exist, the value returned will either be null or "" (the empty string)

Note: When called on an HTML element in a DOM flagged as an HTML document, getAttribute() lower-cases its argument before proceeding.

~~~javascript
var div1 = document.getElementById("div1");
// align is a string containing the value of attributeName
// "align" is the name of the attribute whose value you wnat to get.
var align = div1.getAttribute("align");

alert(align); // shows the value of align for the element with id="div1"
~~~

10）获取和设置元素属性

~~~javascript
// Getting and setting the values of element attributes 
function attr( elem, name, value ) {
	// Make sure that a valid name was provided
	if ( ! name || name.constructor != String ) return '';
	// Figure out if the name is one of the weird naming cases
	name = { 'for': 'htmlFor', 'class': 'className' }[name] || name;
	
	// If the user is setting a value, also
	if ( typeof value != 'undefined' ) {
		elem[name] = value;
	}
	// If we can, use setAttribute
	if ( elem.setAttribute ) {
		elem.setAttribute( name, value );
	}
	// Return the value of the attribute
	return elem[name] || elem.getAttribute( name ) || '';
}
~~~

10) 创建DOM元素

~~~javascript
function create( elem ) {
	return document.createElementNS ? 
		document.createElementNS( 'http://www.w3.org/1999/xhtml', elem ) :
	    document.createElement( elem );
}
~~~ 

Note: **Document.createTextNode()** creates a new text node.

~~~javascript
// text is a Text node.
// data is a string containing the data to be put in the text node
var text = document.createTextNode(data);

function addTextNode(text) {
	var newTxt = document.createTextNode(text),
		p1 = document.getElementById('p1');
	p1.appendChild(newTxt);	
}
~~~

~~~html
<button onclick="addTextNode('YES!');">YES</button>
<p id="p1">First line of paragraph.</p>
~~~

11) 插入元素到DOM

~~~javascript
// A function for inserting an element before another element
function before( parent, before, elem ) {
	// Check to see if no parent node was provided
	if ( elem ) {
		elem = before;
		before = parent;
		parent = before.parentNode;
	}
	parent.insertBefore( checkElem( elem ), before );
}
// A function for appending an element as a child of another element
function append( parent, elem ) {
	parent.appendChild( checkElem( elem ) );
}

// A helper function for the before and append() functions
function checkElem( elem ) {
	// If only a string was provided, convert it into a Text Node
	return elem && elem.constructor == String ? document.createTextNode( elem ) : elem;
}
~~~

**Node.insertBefore** This method inserts the specified node before the reference node as a child of the current node.

If referenceNode is null, the newNode is inserted at the end of the list of child nodes.

Note that referenceNode is not an optional parameter -- you must explicitly pass a Node or null. Failing to provide it or passing invalid values may behave differently in different browser versions.

The returned value is the inserted node.

~~~javascript
// Converting an array of mixed DOM Node/HTML string arguments into a pure array of DOM nodes (转化一个DOM节点/HTML字符串混合型参数数组为纯粹的DOM节点数组)
function checkElem( a ) {
	var r = [];
	// Force the argument into an array, if it isn't already
	if ( a.constructor != Array ) a = [ a ];

	for ( var i = 0; i < a.length; i++ ) {
		// If there's a string
		if ( a[i].constructor == String ) {
			// Create a temporary element to house the HTML
			var div = document.createElement( 'div' );

			// Inject the HTML, to convert it into a DOM structure
			div.innerHTML = a[i];
			// Extract the DOM structure back out of the temp DIV
			for ( var j = 0; j < div.childNodes.length; j++ ) {
				r[r.length] = div.childNodes[j];
			}
		} else if ( a[i].length ) {
			// Assume that it's an array of DOM nodes
			for ( var j = 0; j < a[i].length; j++ ) {
				r[r.length] = a[i][j];
			}
		} else { // Otherwise, assume it's a DOM node
			r[r.length] = a[i];
		}
	}
	return r;
}

// Enhanced functions for inserting and appending into the DOM
function before( parent, before, elem ) {
	// Check to see if no parent node was provided
	if ( elem == null ) {
		elem = before;
		before = parent;
		parent = before.parentNode;
	} 

	// Get the new array of elements
	var elems = checkElem( elem );

	// Move through the array backwards,
	// because we're prepending elements
	for ( var i = elems.length - 1; i >= 0; i-- ) {
		parent.insertBefore( elems[i], before );
	}
}

function append( parent, elem ) {
	// Get the array of elements
	var elems = checkElem( elem );

	// Append them all to the element
	for ( var i = 0; i <= elems.length; i++ ) {
		parent.appendChild( elems[i] );
	}
}

// Example
append( tag("ol")[0], "<li>Mouse trap.</li>";
~~~

~~~html
<ol>
	<li>Cats.</li>
	<li>Dogs.</li>
	<li>Mice.</li>
</ol>
<!--  改变后 -->
<ol>
	<li>Cats.</li>
	<li>Dogs.</li>
	<li>Mice.</li>
	<li>Mice trap.</li>
</ol>
~~~

12) Removing nodes from the DOM

~~~javascript
// Function for removing a node from the DOM
// Remove a single node from the DOM
function remove( elem ) {
	if ( elem ) elem.parentNode.removeChild( elem );
}

// Remove all of an element's children from the DOM
function empty( elem ) {
	while ( elem.firstChild ) {
		remove( elem.firstChild );
	}
}
~~~

***

## XPath

XPath stands for XML Path Language. It uses a non-XML syntax to provide a flexible way of addressing (pointing to) different parts of an XML document. It can also be used to test addressed nodes within a document to determine whether they match a pattern or not.

XPath is mainly used in XSLT, but can also be used as a much more powerful way of navigating through the DOM of any XML-like language document, such as HTML and XUL, instead of relying on the document.getElementById method, the element.childNodes properties, and other DOM Core features.

* [Introduction to using XPath in Javascript](https://developer.mozilla.org/en/docs/Introduction_to_using_XPath_in_JavaScript)
* [XPath 1.0](http://www.w3.org/TR/xpath)
* [XPath 2.0](http://www.w3.org/TR/xpath20)

Constant        		| CSS           | XPath
-------------       | ------------- | -------------  
所有元素  		   	   | *   			      | //* |
所有`<p>`元素  	   | p   			      | //p |
所有子元素  		   | p> *   			   | //p/* |
由ID获取元素  		   | #foo   			   | //\*[@id='foo'] |
由类获取元素  		   | .foo   			   | //\*[contains(@class,'foo')] |
由属性获取元素 	      | \*[title]   	   | //\*[@title] |
`<p>`的第一个子元素   | \*[title]   	   | //p\*[0] |
所有拥有子元素的`<p>` | p >\*:first-child | //p/[a] |
下一个元素  		   | p + *   			   | //p/下一个兄弟元素::*[0] |


Note: XPath 是一门用于选取 XML 文档的部件的语言。XPath 被设计为供 XSLT、XQuery 以及 XPointer 使用。XPath 使用路径表达式来选取 XML 文档中的节点或者节点集。这些路径表达式和我们在常规的电脑文件系统中看到的表达式非常相似。XPath 含有超过 100 个内建的函数。这些函数用于字符串值、数值、日期和时间比较、节点和 QName 处理、序列处理、逻辑值等等。他提供了对 string，number，booleans 基本数据类型的操作功能。 XPath 使用类似于普通的文件系统寻址方式，对 XML 中的数据进行匹配。并且 XPath 还提供很多标准库函数，以进行更多复杂的处理操作。

***

## Convert special characters to HTML in Javascript

~~~javascript
/**
 * The best way in my opinion is to use the browser's 
 * inbuilt HTML escape functionality to handle many of  
 * the cases. To do this simply create a element in the 
 * DOM tree and set the innerText of the element to your 
 * string. Then retrieve the innerHTML of the element. 
 * The browser will return an HTML encoded string.
 * Note: You will still need to escape quotes (double 
 * and single) yourself. You can use any of the methods 
 * outlined by others here. 
 *
 * @param string s
 * @return string
 */
function htmlEncode( s ) {
	var el = document.createElement( 'div' );
	el.innerText = el.textContent = s;
	s = el.innerHTML;
	return s;
}

// Test run:
htmlEncode( '&;\'><"' );

// Output: &amp;;'&gt;&lt;"

// This generic function encodes every non alphabetic character to its htmlcode (numeric)
function HTMLEncode(str){
  var i = str.length,
      aRet = [];

  while (i--) {
    var iC = str[i].charCodeAt();
    if (iC < 65 || iC > 127 || (iC>90 && iC<97)) {
      aRet[i] = '&#'+iC+';';
    } else {
      aRet[i] = str[i];
    }
   }
  return aRet.join('');    
}

/**
 * Note that charCodeAt will always return a value that 
 * is less than 65,536. This is because the higher code 
 * points are represented by a pair of (lower valued) 
 * "surrogate" pseudo-characters which are used to 
 * comprise the real character. Because of this, in 
 * order to examine or reproduce the full character for 
 * individual characters of value 65,536 and above, for 
 * such characters, it is necessary to retrieve not only 
 * charCodeAt(i), but also charCodeAt(i+1) (as if 
 * examining/reproducing a string with two >letters).
 *
 * (c) 2012 Steven Levithan <http://slevithan.com/>
 * MIT license
 */
if (!String.prototype.codePointAt) {
    String.prototype.codePointAt = function (pos) {
        pos = isNaN(pos) ? 0 : pos;
        var str = String(this),
            code = str.charCodeAt(pos),
            next = str.charCodeAt(pos + 1);
        // If a surrogate pair
        if (0xD800 <= code && code <= 0xDBFF && 0xDC00 <= next && next <= 0xDFFF) {
            return ((code - 0xD800) * 0x400) + (next - 0xDC00) + 0x10000;
        }
        return code;
    };
}

/**
 * Encodes special html characters
 * @param string
 * @return {*}
 */
function html_encode(string) {
    var ret_val = '';
    for (var i = 0; i < string.length; i++) { 
        if (string.codePointAt(i) > 127) {
            ret_val += '&#' + string.codePointAt(i) + ';';
        } else {
            ret_val += string.charAt(i);
        }
    }
    return ret_val;
}

// Usage example: html_encode("✈");

// Create a function that uses string replace
function convert(str) {
  str = str.replace(/&/g, "&amp;");
  str = str.replace(/>/g, "&gt;");
  str = str.replace(/</g, "&lt;");
  str = str.replace(/"/g, "&quot;");
  str = str.replace(/'/g, "&#039;");
  return str;
}

/**
 *  Usage: ConvChar('<-"-&-"->-<-\'-#-\'->')
 *  Result: &lt;-&quot;-&amp;-&quot;-&gt;-&lt;-&#039;-
 *  &#035;-&#039;-&gt;
 */
function ConvChar( str ) {
  c = {'<':'&lt;', '>':'&gt;', '&':'&amp;', '"':'&quot;', "'":'&#039;',
       '#':'&#035;' };
  return str.replace( /[<&>'"#]/g, function(s) { return c[s]; } );
}

function char_convert() {

    var chars = ["©","Û","®","ž","Ü","Ÿ","Ý","$","Þ","%","¡","ß","¢","à","£","á","À","¤","â","Á","¥","ã","Â","¦","ä","Ã","§","å","Ä","¨","æ","Å","©","ç","Æ","ª","è","Ç","«","é","È","¬","ê","É","­","ë","Ê","®","ì","Ë","¯","í","Ì","°","î","Í","±","ï","Î","²","ð","Ï","³","ñ","Ð","´","ò","Ñ","µ","ó","Õ","¶","ô","Ö","·","õ","Ø","¸","ö","Ù","¹","÷","Ú","º","ø","Û","»","ù","Ü","@","¼","ú","Ý","½","û","Þ","€","¾","ü","ß","¿","ý","à","‚","À","þ","á","ƒ","Á","ÿ","å","„","Â","æ","…","Ã","ç","†","Ä","è","‡","Å","é","ˆ","Æ","ê","‰","Ç","ë","Š","È","ì","‹","É","í","Œ","Ê","î","Ë","ï","Ž","Ì","ð","Í","ñ","Î","ò","‘","Ï","ó","’","Ð","ô","“","Ñ","õ","”","Ò","ö","•","Ó","ø","–","Ô","ù","—","Õ","ú","˜","Ö","û","™","×","ý","š","Ø","þ","›","Ù","ÿ","œ","Ú"]; 
    var codes = ["&copy;","&#219;","&reg;","&#158;","&#220;","&#159;","&#221;","&#36;","&#222;","&#37;","&#161;","&#223;","&#162;","&#224;","&#163;","&#225;","&Agrave;","&#164;","&#226;","&Aacute;","&#165;","&#227;","&Acirc;","&#166;","&#228;","&Atilde;","&#167;","&#229;","&Auml;","&#168;","&#230;","&Aring;","&#169;","&#231;","&AElig;","&#170;","&#232;","&Ccedil;","&#171;","&#233;","&Egrave;","&#172;","&#234;","&Eacute;","&#173;","&#235;","&Ecirc;","&#174;","&#236;","&Euml;","&#175;","&#237;","&Igrave;","&#176;","&#238;","&Iacute;","&#177;","&#239;","&Icirc;","&#178;","&#240;","&Iuml;","&#179;","&#241;","&ETH;","&#180;","&#242;","&Ntilde;","&#181;","&#243;","&Otilde;","&#182;","&#244;","&Ouml;","&#183;","&#245;","&Oslash;","&#184;","&#246;","&Ugrave;","&#185;","&#247;","&Uacute;","&#186;","&#248;","&Ucirc;","&#187;","&#249;","&Uuml;","&#64;","&#188;","&#250;","&Yacute;","&#189;","&#251;","&THORN;","&#128;","&#190;","&#252","&szlig;","&#191;","&#253;","&agrave;","&#130;","&#192;","&#254;","&aacute;","&#131;","&#193;","&#255;","&aring;","&#132;","&#194;","&aelig;","&#133;","&#195;","&ccedil;","&#134;","&#196;","&egrave;","&#135;","&#197;","&eacute;","&#136;","&#198;","&ecirc;","&#137;","&#199;","&euml;","&#138;","&#200;","&igrave;","&#139;","&#201;","&iacute;","&#140;","&#202;","&icirc;","&#203;","&iuml;","&#142;","&#204;","&eth;","&#205;","&ntilde;","&#206;","&ograve;","&#145;","&#207;","&oacute;","&#146;","&#208;","&ocirc;","&#147;","&#209;","&otilde;","&#148;","&#210;","&ouml;","&#149;","&#211;","&oslash;","&#150;","&#212;","&ugrave;","&#151;","&#213;","&uacute;","&#152;","&#214;","&ucirc;","&#153;","&#215;","&yacute;","&#154;","&#216;","&thorn;","&#155;","&#217;","&yuml;","&#156;","&#218;"];

    for(x=0; x<chars.length; x++){
        for (i=0; i<arguments.length; i++){
            arguments[i].value = arguments[i].value.replace(chars[x], codes[x]);
        }
    }
 }

char_convert(this);
// As was mentioned by dragon the cleanest way to do it is with jQuery:
function HtmlEncode(s) {
    return $('<div>').text(s).html();
}

function HtmlDecode(s) {
    return $('<div>').html(s).text();
}

function escape (text) {
  return text.replace(/[<>\&\"\']/g, function(c) {
    return '&#' + c.charCodeAt(0) + ';';
  });
}

alert(escape("<>&'\""));
~~~

**用Javascript进行HTML转义**


[](http://www.cnblogs.com/hoodng/archive/2012/03/16/2399543.html)

## Event 

[JS-事件冒泡、事件捕获和事件代理](http://oakland.github.io/2016/06/14/JS-%E4%BA%8B%E4%BB%B6%E5%86%92%E6%B3%A1%E3%80%81%E4%BA%8B%E4%BB%B6%E6%8D%95%E8%8E%B7%E5%92%8C%E4%BA%8B%E4%BB%B6%E4%BB%A3%E7%90%86/)

[js的事件机制](http://www.w3cfuns.com/notes/17398/8062de2558ef495ce6cb7679f940ae5c.html)

[DOM 事件深入浅出（一）](http://www.jianshu.com/p/8c41a302bb17)

[JS事件模型](https://segmentfault.com/a/1190000006934031)

[W3C规范](https://www.w3.org/html/ig/zh/wiki/%E7%BF%BB%E8%AF%91)

**事件循环**

JavaScript的单线程，与它的用途有关。作为浏览器脚本语言，JavaScript的主要用途是与用户互动，以及操作DOM。这决定了它只能是单线程，否则会带来很复杂的同步问题。比如，假定JavaScript同时有两个线程，一个线程在某个DOM节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？

javascript基于任务队列，分为2种任务形式：同步(synchronous)和异步(asynchronous) 同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；异步任务指的是，不进入主线程、而进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

异步执行的运行机制：

1. 所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。
2. 主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。
3. 一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
4. 主线程不断重复上面的第三步。

![主线程和任务队列的示意图](http://image.beekka.com/blog/2014/bg2014100801.jpg)

**事件冒泡、事件捕获**

1. 如果想要在一个 HTML 元素上同时注册两个事件，必须用 DOM 2级规范的方式来进行事件注册，而不能用 DOM 0 级规范的方式，因为 DOM 0 级规范的方式添加两个事件的话，后面的事件处理器会覆盖前面的事件处理器
2. 在冒泡和捕获事件同时存在时，谁先注册谁先执行

<font color="#0095dd">**event.target**</font> A reference to the object that dispatched the event. It is different from event.currentTarget when the event handler is called during the bubbling or capturing phase of the event. （通俗点解释就是，只有当绑定的事件处理程序与触发该事件处理程序都为同一个对象的时候，两者相同。）

The event.target property can be used in order to implement event delegation.

~~~javascript
// Assuming there is a 'list' variable containing an instance of an HTML ul element.
function hide(e) {
  // Unless list items are separated by a margin, e.target should be different than e.currentTarget
  e.target.style.visibility = 'hidden';
}

list.addEventListener('click', hide, false);

// If some element (<li> element or a link within an <li> element for instance) is clicked, it will disappear.
// It only requires a single listener to do that
~~~

<font color="#0095dd">**event.currentTarget**</font> Identifies the current target for the event, as the event traverses the DOM. It always refers to the element the event handler has been attached to as opposed to event.target which identifies the element on which the event occurred. (该属性总是指向被绑定事件句柄（event handler）的元素。而与之对比的event.target ，则是指向触发该事件的元素)

event.currentTarget is interesting to use when attaching the same event handler to several elements.

~~~javascript
function hide(e){
  e.currentTarget.style.visibility = "hidden";
  // When this function is used as an event handler: this === e.currentTarget
}

var ps = document.getElementsByTagName('p');

for(var i = 0; i < ps.length; i++){
  ps[i].addEventListener('click', hide, false);
}

// click around and make paragraphs disappear
~~~

Note: On Internet Explorer 6 through 8, the event model is different. Event listeners are attached with the non-standard EventTarget.attachEvent method. In this model, there is no equivalent to event.currentTarget and this is the global object. One solution to emulate the event.currentTarget feature is to wrap your handler in a function calling the handler using Function.prototype.call with the element as a first argument. This way, this will be the expected value.

**事件阶段**

* A tabbed-navigation scenario with hovering effects

~~~javascript
// Find all the <li> elements, to attach the event handlers to them
var li = document.getElementsByTagName('li');
for( var i = 0; i < li.length; i++) {
   // Attach a mouseover event handler to the <li> element,
   // which changes the <li>s background to grey.
	li[i].onmouseover = function() {
		this.style.backgroundColor = '#f1f1f1';
	};

	// Attach a mouseover event handler to the <li> element
	// which changes the <li>s background back to its default white
	li[i].onmouseout = function() {
		this.style.backgroundColor = 'white';
	}
}
~~~

~~~html
<ul>
	<li><a href="">Home</a></li>
	<li><a href="">About</a></li>
	<li><a href="">Products</a></li>
</ul>
~~~

Note: 当鼠标悬停于`<a>`(并未监听事件)上会冒泡到其父`<li>`元素，而<li>监听事件触发效果。

<font color="#0095dd">**Event.stopPropagation()**</font> Prevents further propagation of the current event in the capturing and bubbling phases.

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
<title>Event Propagation</title>

<style>
#t-daddy { border: 1px solid red }
#c1 { background-color: pink; }
</style>

<script>
function stopEvent(ev) {
  c2 = document.getElementById("c2");
  c2.innerHTML = "hello";

  // this ought to keep t-daddy from getting the click.
  ev.stopPropagation();
  alert("event propagation halted.");
}

function load() {
  elem = document.getElementById("tbl1");
  elem.addEventListener("click", stopEvent, false);
}
</script>
</head>

<body onload="load();">

<table id="t-daddy" onclick="alert('hi');">
  <tr id="tbl1">
    <td id="c1">one</td>
  </tr>
  <tr>
    <td id="c2">two</td>
  </tr>
</table>

</body>
</html>
~~~

<font color="#0095dd">**Event.stopImmediatePropagation()**</font> Prevents other listeners of the same event from being called. (阻止当前事件的冒泡行为并且阻止当前事件所在元素上的所有相同类型事件的事件处理函数的继续执行.)

Note: If several listeners are attached to the same element for the same event type, they are called in order in which they have been added. If during one such call, event.stopImmediatePropagation() is called, no remaining listeners will be called. (如果某个元素有多个相同类型事件的事件监听函数,则当该类型的事件触发时,多个事件监听函数将按照顺序依次执行.如果某个监听函数执行了 event.stopImmediatePropagation()方法,则除了该事件的冒泡行为被阻止之外(event.stopPropagation方法的作用),该元素绑定的其余相同类型事件的监听函数的执行也将被阻止.)

~~~html
<!DOCTYPE html>
<html>
    <head>
        <style>
            p { height: 30px; width: 150px; background-color: #ccf; }
            div {height: 30px; width: 150px; background-color: #cfc; }
        </style>
    </head>
    <body>
        <div>
            <p>paragraph</p>
        </div>
        <script>
            document.querySelector("p").addEventListener("click", function(event)
            {
                alert("我是p元素上被绑定的第一个监听函数");
            }, false);
            document.querySelector("p").addEventListener("click", function(event)
            {
                alert("我是p元素上被绑定的第二个监听函数");
                event.stopImmediatePropagation();
                //执行stopImmediatePropagation方法,阻止click事件冒泡,并且阻止p元素上绑定的其他click事件的事件监听函数的执行.
            }, false);
            document.querySelector("p").addEventListener("click", function(event)
            {
                alert("我是p元素上被绑定的第三个监听函数");
                //该监听函数排在上个函数后面,该函数不会被执行.
            }, false);
            document.querySelector("div").addEventListener("click", function(event)
            {
                alert("我是div元素,我是p元素的上层元素");
                //p元素的click事件没有向上冒泡,该函数不会被执行.
            }, false);
        </script>
    </body>
</html>
~~~

[W3C Document Object Model Events](https://www.w3.org/TR/DOM-Level-2-Events/events.html#Events-flow-capture)

**事件代理**

在编程中，如果我们不想或不能够直接操纵目标对象，我们可以利用delegate创建一个代理对象来调用目标对象的方法，从而达到操纵目标对象的目的。毋庸置疑，代理对象要拥有目标对象的引用。

~~~html
<!doctype html>
<html dir="ltr" lang="zh-CN">
  <head>
    <meta charset="utf-8"/>
    <meta http-equiv="X-UA-Compatible" content="IE=Edge">
    <script type="text/javascript">
      var ClassA = function(){
        var _color = "red";
        return {
          getColor : function(){
            document.write("<p>ClassA的实例的私有属性_color目前是<span style='color:"+_color+"' >"+_color+"</span></p>");
          },
          setColor:function(color){
            _color = color;
          }
        };
      };
      var delegate = function (client,clientMethod ){
        return function() { 
        		// 也可以：return client[clientMethod].apply(client, arguments); 
        		// 调用: var d = delegate(a, "setColor");
        		return clientMethod.apply(client,arguments); 
        }
      }
      window.onload = function(){
        var a = new  ClassA();
        a.getColor();
        a.setColor("green");
        a.getColor();
        //alert(a._color);
        document.write("<p>执行代理！</p>")
        var d = delegate(a,a.setColor);
        d("blue");
        document.write("<p>执行完毕！</p>")
        a.getColor();
      };
    </script>
    <title>delegate</title>
  </head>
  <body>
  </body>
</html>
~~~html

~~~javascript
Function.prototype.delegate = function(delegateFor) {
			return delegateFor.apply(null, arguments);
		}

		var Hoge = function() {
			this.open = funciton() {
				return 'this is Hoge#open';
			}
			this.close = function() {
				return 'this is Hoge#close';
			}
		}

		var Foo = function() {
			this.name = 'foo';
			this.open = function() {
				return 'this is Foo#open';
			}
			this.close = function() {
				return 'this is Foo#close';
			}
		}

		var hoge = new Hoge();
		var Foo  = new Foo();

		console.log(hoge.open());
		console.log(hoge.open.delegate(foo.open)); // this is Foo#open
		console.log(foo.open.delegate(hoge.open)); // this is Hoge#open
~~~

由于delegate实现目标对象的隐藏，这对于我们保护一些核心对象是非常有用的。实质上是通过js中的apply和call方法改边函数内部this的作用域。此处实现的是实例的委托而不是类的委托，可参考jQuery实现。

**DOM事件流**

“DOM2级事件”规定的事件流包括三个阶段：事件捕获阶段==>处于目标阶段==>事件冒泡阶段。首先发生的是事件捕获阶段，为截获事件提供了机会。然后是实际的目标接收事件。最后一个阶段是冒泡阶段，

![HTMLElement Inheritance](data:image/png;base64,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)

**取消事件冒泡**

~~~javascript
// A generic function for stopping event bubbling
function stopBubble( e ) {
	// If an event object is provided, then this is a non-IE browser
	if ( e && e.stopPropagation ) {
		// and therefore it supports the W3C stopPropagation() method
		e.stopPropagation();
	} else {
		// Otherwise, we need to use the Internet Explorer
		// way of cancelling event bubbling
		window.event.cancelBubble = true;
	}
}

// A generic function for preventing the default browser action from occurring
function stopDefault() {
	// Prevent the default browser action (W3C)
	if ( e && e.preventDefault ) {
		e.preventDefault();
	} else {
		// A shortcut for stoping the browser action in IE
		window.event.returnValue = false;
	}
	
	return false;
}
~~~

**监听CSS何时启用**

Providing a fade-in-on-load technique without failing if javascript is disabled (针对javascript被关闭或不支持的情况)

~~~html
<!-- The instant the script is run, a new class is attached to the <html> element giving us the ability to know if Javascript is enabled, or not. -->
<script>document.documentElement.className = 'js';</script>

<!-- If Javascript is enabled, hide the block of text, which we will fade in later. -->
<style>.js #fadein{display: none}</style>
<body>
	<div id="fadein">Block of stuff to fade in...</div>
</body>
~~~

***

## 获取CSS
A function for finding the actual computed value of a CSS style property on an element

~~~javascript
// Get a style property (name) of a specific element (elem)
function getStyle( elem, name ) {
	// If the propertyh exists in style[], then it's been set recently (and is current)
	if ( elem.style[name] ) {
		return elem.style[name];
	}
	
	// Otherwise, try to use IE's method
	else if ( elem.currentStyle ) {
		return elem.currentStyle[name];
	}
	
	else if ( document.defaultView && document.defaultView.getComputedStyle ) {
		// It uses the traditional 'text-align' style of rule writing,
		// instead of textAlign
		name = name.replace( /[A-Z]/g, "-$1" );
		name = name.toLowerCase();
		
		// Get the style object and get the value of the property (if it exists) 
		var s = document.defaultView.getComputedStyle( elem, "" );
		return s && s.getPropertyValue( name );
	} else {
		// Otherwise, we're using some other browser
		return null;
	}
}
~~~

<font color="#217ac0">document.defaultView</font> In browsers, **document.defaultView** returns the window object associated with a document, or null if none is available. (This property is read-only)


###void operator

判断house对象是否存在，然后再判断house.dogs是否存在，最后取house.dogs[0],一般会这样写：

~~~javascript
var dog = (typeof house !== 'undefined && house !== null) && house.dogs && house.dogs[0]
~~~

在coffee中可以这么写 `dog = house?.dogs?[0];` 其生成的js代码为：

~~~javascript
var dog, _ref;

dog = (typeof house !== "undefined" && house !== null) ? 
        ((_ref = house.dogs) != null ? _ref[0] : void 0 ) 
        : void 0;
~~~

* 如果house未定义或house为null时，返回void 0
* 如果house.dogs为null时，返回void 0

void 0的值：、

~~~javascript
typeof void 0 //得到"undefined"
console.log(void 0) //输出undefined
~~~

那么 `void 100, void hello(), void i++` 这无数可能组合的值是什么？

规范ECMAScript 262对此如下描述：

The void Operator

The production UnaryExpression : void UnaryExpression is evaluated as follows:  (产生式 UnaryExpression : void UnaryExpression 按如下流程解释)

* Let expr be the result of evaluating UnaryExpression. (令 expr 为解释执行UnaryExpression的结果)
* Call GetValue(expr). (调用 GetValue(expr))
* Return undefined. (返回 undefined)

NOTE: GetValue must be called even though its value is not used because it may have observable side-effects. (GetValue一定要调用，即使它的值不会被用到，但是这个表达式可能会有副作用(side-effects))

重点在于：无论void后的表达式是什么，void操作符都会返回undefined.
既然(void 0) === undefined，那直接写undefined不就行了么？

**为什么要用void？**

因为undefined在javascript中不是保留字。换言之，你可以写出：

~~~javascript
function joke() {
    var undefined = "hello world";
    console.log(undefined); //会输出"hello world"
}
console.log(undefined); //输出undefined
~~~

你可以在一个函数上下文内以undefined做为变量名，于是在这个上下文写的代码便只能通过从全局作用域来取到undefined，如：

~~~javascript
window.undefined //浏览器环境
GLOBAL.undefined //Node环境
~~~

但要注意的是，即便window, GLOBAL仍然可以在函数上下文被定义，故从window/GLOBAL上取undefined并不是100%可靠的做法。如：

~~~javascript
function x() {
   var undefined = 'hello world',
       f = {},
       window = {
           'undefined': 'joke'
       };
   console.log(undefined);// hello world
   console.log(window.undefined); //joke
   console.log(f.a === undefined); //false
   console.log(f.a === void 0); //true
}
~~~

于是，采用void方式获取undefined便成了通用准则。如underscore.js里的isUndefined便是这么写的：

~~~javascript
_.isUndefined = function(obj) {
    return obj === void 0;
}
~~~

除了采用void能保证取到undefined值以外，还有其它方法吗？有的，还有一种方式是通过函数调用。如AngularJS的源码里就用这样的方式：

~~~javascript
(function(window, document, undefined) {
    //.....
})(window, document);
~~~

通过不传参数，确保了undefined参数的值是一个undefined。

**其它作用**

除了取undefined外，void还有什么其它用处吗？

还有一个常见的功能，填充href。下面是一个微博截图，它的转发, 收藏， 讨论都是超链接，但是用户并不希望点击它们会跳转到另一个页面，而是引发出一些交互操作。

理论上而言，这三个超链接都是没有URL的，但如果不写的话，呵呵，点击它会刷新整个页面。于是便用上了href="javascript:void(0)的方式，确保点击它会执行一个纯粹无聊的void(0)。

另一种情况是，如果我们要生成一个空的src的image，最好的方式似乎也是src='javascript:void(0)'，参见StackOverflow上的这个问题：[What's the valid way to include an image with no src?](http://stackoverflow.com/questions/5775469/whats-the-valid-way-to-include-an-image-with-no-src)

回到void的定义，有一句话特别让人迷惑：

`注意：GetValue一定要调用，即使它的值不会被用到，但是这个表达式可能会有副作用(side-effects)。`

这是什么意思？这表示无论void右边的表达式是什么，都要对其求值。这么说可能不太明白，在知乎上winter大神有过阐述关于js中void，既然返回永远是undefined，那么GetValue有啥用？，我且拾人牙慧，代入一个场景，看代码：

~~~javascript
var happiness = 10;
var girl = {
    get whenMarry() {
        happiness--;
        return 1/0; //Infinity
    },
    get happiness() {
        return happiness;
    }
};

console.log(girl.whenMarry); //调用了whenMarry的get方法
console.log(girl.happiness); // 9

void girl.whenMarry; //调用了whenMarry的get方法
console.log(girl.happiness); // 8

delete girl.whenMarry; //没有调用whenMarry的get方法
console.log(girl.happiness); //还是8
~~~

上述代码定义了一个大龄文艺女青年，每被问到什么时候结婚呀(whenMarry)，happiness都会减1。从执行情况可以看出，无论是普通访问girl.whenMarry，还是void girl.whenMarry都会使她的happiness--。而如果把void换成delete操作符写成delete girl.whenMarry，她的happiness就不会减了，因为delete操作符不会对girl.whenMarry求值。

**总结**

void有如下作用：

* 通过采用void 0取undefined比采用字面上的undefined更靠谱更安全，应该优先采用void 0这种方式。
* 填充`<a>`的href确保点击时不会产生页面跳转; 填充`<image>`的src，确保不会向服务器发出垃圾请求。

###图解原型对象和原型链

1、prototype和__proto__的区别

![Difference between prototype and __proto__](http://ac-Myg6wSTV.clouddn.com/2e7817d676e605e54e62.png)

~~~javascript
var a = {};
console.log(a.prototype); //undefined
console.log(a.__proto__);  //Object {}

var b = function(){}
console.log(b.prototype); //b {}
console.log(b.__proto__);  //function() {}
~~~

2、__proto__属性指向谁

![__proto__](http://ac-Myg6wSTV.clouddn.com/414693e5821245adeb86.png)

~~~javascript
/*1、字面量方式*/
var a = {};
console.log(a.__proto__); //Object {}
console.log(a.__proto__ === a.constructor.prototype); //true

/*2、构造器方式*/
var A = function(){}; var a = new A();
console.log(a.__proto__); //A {}
console.log(a.__proto__ === a.constructor.prototype); //true

/*3、Object.create()方式*/
var a1 = {a:1} 
var a2 = Object.create(a1);
console.log(a2.__proto__); //Object {a: 1}
console.log(a.__proto__ === a.constructor.prototype); //false（此处即为图1中的例外情况）</code></pre>
~~~

3、什么是原型链

![prototype chain](http://ac-Myg6wSTV.clouddn.com/e8f4cc45af11650de1f8.png)

~~~javascript
var A = function(){};
var a = new A();
console.log(a.__proto__); //A {}（即构造器function A 的原型对象）
console.log(a.__proto__.__proto__); //Object {}（即构造器function Object 的原型对象）
console.log(a.__proto__.__proto__.__proto__); //null
~~~

###Function 和 Object 的关系及简述 instanceof 运算符

1、instanceof究竟是运算什么的？

~~~javascript
Function instanceof Object;//true
Object instanceof Function;//true
~~~

曾经简单理解 instanceof 只是检测一个对象是否是另个对象 new 出来的实例（例如var a = new Object()，a instanceof Object返回true），但实际 instanceof 的运算规则上比这个更复杂。

[W3C 解释](https://www.w3.org/html/ig/zh/wiki/ES5/%E8%A1%A8%E8%BE%BE%E5%BC%8F#instanceof_.E8.BF.90.E7.AE.97.E7.AC.A6) (I don't understand this at all.)

[js相关文章](https://www.zhihu.com/people/chenyulu/topic/19552521/answers)

~~~javascript
//假设instanceof运算符左边是L，右边是R
L instanceof R //instanceof运算时，通过判断L的原型链上是否存在R.prototype
L.__proto__.__proto__ ..... === R.prototype ？ //如果存在返回true 否则返回false
~~~

注意：instanceof 运算时会递归查找L的原型链，即 L.__proto__.__proto__.__proto__.__proto__... 直到找到了或者找到顶层为止。

所以一句话理解 instanceof 的运算规则为：

instanceof 检测左侧的 __proto__ 原型链上，是否存在右侧的 prototype 原型。

2、图解构造器Function和Object的关系

![](http://ac-Myg6wSTV.clouddn.com/f867ad406581d9a55505.png)

~~~javascript
//①构造器Function的构造器是它自身
Function.constructor=== Function;//true

//②构造器Object的构造器是Function（由此可知所有构造器的constructor都指向Function）
Object.constructor === Function;//true

//③构造器Function的__proto__是一个特殊的匿名函数function() {}
console.log(Function.__proto__);//function() {}

//④这个特殊的匿名函数的__proto__指向Object的prototype原型。
Function.__proto__.__proto__ === Object.prototype//true

//⑤Object的__proto__指向Function的prototype，也就是上面③中所述的特殊匿名函数
Object.__proto__ === Function.prototype;//true
Function.prototype === Function.__proto__;//true
~~~

3、当构造器Object和Function遇到instanceof

我们回过头来看第一部分那个“奇怪的现象”，从上面那个图中我们可以看到：

~~~javascript
Function.__proto__.__proto__ === Object.prototype;//true
Object.__proto__ === Function.prototype;//true
~~~

所以再看回第一点中我们说的 instanceof 的运算规则，Function instanceof Object 和 Object instanceof Function 运算的结果当然都是true啦！

如果看完以上，你还觉得上面的关系看晕了的话，只需要记住下面两个最重要的关系，其他关系就可以推导出来了：

所有的构造器的 constructor 都指向 Function

Function 的 prototype 指向一个特殊匿名函数，而这个特殊匿名函数的 __proto__ 指向 Object.prototype

[从一行CSS调试代码中学到的JavaScript知识](http://ourjs.com/detail/54be0a98232227083e000012)

[Javascript BOM](http://gold.xitu.io/post/583437d8128fe1006ccffde8)

## 函数节流 (throttle)

作用： 为了让一个函数执行不必太频繁，减少一些过快的调用来节流

场景：针对DOM事件监听所触发的回调
For Example: 实现非H5的拖拽，当触发mousemove事件，在对调中获取事件元素的位置，并重设其位置所导致的页面重排与重绘，可能会导致浏览器失去响应或卡顿，造成用户体验的下降，此时需要降低触发回调的频率，比如500ms一次，但此阈值不能太大(容易造成失真), 太小会导致性能太差，这样的解决方案称之为节流。

* DOM 元素的拖拽功能实现（mousemove）
* 射击游戏的 mousedown/keydown 事件（单位时间只能发射一颗子弹）
* 计算鼠标移动的距离（mousemove）
* Canvas 模拟画板功能（mousemove）
* 搜索联想（keyup）
* 监听滚动事件判断是否到页面底部自动加载更多：给 scroll 加了 debounce 后，只有用户停止滚动后，才会判断是否到了页面底部；如果是 throttle 的话，只要页面滚动就会间隔一段时间判断一次


## 函数去抖

作用：对于一定时间段连续函数的调用，只执行一次

场景：

* 每次 resize/scroll 触发统计事件
* 文本输入的验证（连续输入文字后发送 AJAX 请求进行验证，验证一次就好） 


