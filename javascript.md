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

![HTMLElement Inheritance](https://raw.githubusercontent.com/dnd2/learngit/master/images/dom_event_fluid.png)

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

![Difference between prototype and __proto__](https://raw.githubusercontent.com/dnd2/learngit/master/images/prototype_figure.png)

~~~javascript
var a = {};
console.log(a.prototype); //undefined
console.log(a.__proto__);  //Object {}

var b = function(){}
console.log(b.prototype); //b {}
console.log(b.__proto__);  //function() {}
~~~

2、__proto__属性指向谁

![__proto__](https://raw.githubusercontent.com/dnd2/learngit/master/images/prototype_figure_2.png)

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

常用函数学习：

1. Object.defineProperty  This method defines a new property directly on an object, or modifies an exisiting property on an object, and returns the object.

`Object.defineProperty(obj, prop, descriptor)`

~~~javascript
/**
 * Below is an example of how to use Object.create() 
 * to achieve classical inheritance. This is for single  
 * inheritance, which is all that Javascript support.
 */
function Shape() {
   this.x = 0;
   this.y = 0;
}
Shape.prototype.move = function(x, y) {
    this.x = x;
    this.y = y;
    console.info('Shape moved.');
}
function Rectangle() {
    Shape.call(this);
}
Rectangle.prototype = Object.create(Shape.prototype);
Rectangle.prototype.constructor = Rectangle;
var rect = new Rectangle();
console.log('Is rect an instance of Rectangle', rect instanceof Rectangle);
console.log('Is rect an instance of Sahpre', rect instanceof Shape);
       rect.move(1, 1);
~~~

**深拷贝和浅拷贝区别**

* 针对对象如Object，Array等
* 浅拷贝只是拷贝属性所指向对象的引用
* 深拷贝会将属性指向的对象完全复制一份成为一个独立的实例
* 如果拷贝的对象层级比较深则可能存在性能问题

[JavaScript 的深复制](http://jerryzou.com/posts/dive-into-deep-clone-in-javascript/)

**解决jQuery重复绑定事件**
[案例](http://bookshadow.com/weblog/2014/08/21/jquery-duplicate-bind-demo-and-solution/)
