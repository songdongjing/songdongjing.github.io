---
title: hexo+github搭建个人博客的心酸历程
date: 2024-04-19 22:24:27
tags: 技巧
---

## 

## 准备工具

* 安装git for windows。[Git (git-scm.com)](https://git-scm.com/)（输入git version进行检测） 
* 安装node.js https://nodejs.cn/download/ (输入 node -v进行检测)
* 安装hexo（输入 hexo -v进行检测，可以用npm工具直接进行安装，后面的章节会讲到）

## 原理

git工具用于本地维护一个git库，上传本地的博客到github服务器中。

hexo工具用于在git库中生成博客框架，按照hexo官网的定义（A fast, simple & powerful blog framework）。

npm是node.js的包管理工具，用于解决ndoe.js代码部署问题，本文内容中是用于安装hexo工具的其定义(Build amazing things)。

Node.js具体用来不太理解，但是本章内容中，下载node.js后，就有了npm工具，从而可以下载hexo工具。

## 实践

由于缺乏对前后端和服务器相关的知识，导致实际部署过程遇到了各种问题，不过最后总算成功了，不过还是可能存在相当多的问题。

### 创建git库

一个项目的总是从创建git库开始的，所以自然需要用到git的各种命令。

首先在github服务器上创建一个新的repository，而且名字必须为<u>yourname.github.io</u>表示这是一个网页。

然后找个文件夹，右键选择open git bash here出现命令窗口。

然后就是建立与github服务器连接过程了如下

```shell
git init #初始化该文件夹为git仓库
#配置身份信息
git config --global user.name "yourname"
git config --global user.email "youremail"
#建立ssh连接(目前github已经不再支持通过输入用户名和密码登录了，只支持ssh或者token的方式，这里我耽误了很久)
ssh-keygen -t rsa -C "yourname@foxmail.com"#这行代码是建立创建一个本机的ssh key，在本地会得到一个密匙和公匙(id_rsa.pub)
#在github服务器中用得到的公匙创建一个ssh钥匙，就完成连接了(应该是密码学的内容，服务器端可以通过公匙解密出密匙)
#接下来就是写md文件和提交等一系列常规操作了
#如果仓库中已经有文件了一般要先进行同步或者拉下来
git stash #如果直接拉有问题，可以试一下这一句
git pull --rebase  #拉代码

git add README.md  #将写好的README.md从工作目录添加到暂存区
git commit -m "first commit"#将代码更改保存到本地版本库中，并且还附带描述性信息
git remote remove origin #断开与仓库的连接
git remote add origin https://github.com/yourname/yourname.github.io.git#将远程仓库origin和本地仓库进行关联
git push -u origin main#将本地分支推送到远程仓库分支
```

### 创建博客框架

前面已经安装了node.js,所以已经有npm工具了。

首先用开启一个命令行，用npm工具安装hexo（如果遇到安装了但是找不到npm命令的情况，请检查环境变量,将git的bin文件夹和npm文件夹(通常在user\AppData\Roaming中)放入用户变量中）

```shell
npm install hexo-cli -g
```

安装完后输入如下命令检查是否安装成功

```shell
hexo -v 
```

然后就是跟git那边类似，在创建的文件夹下输入如下命令

```shell
hexo init #在该文件夹下搭建博客框架，会出现很多文件，最重要的为_config.yml
npm install  #自动安装所需组件（这个不知道对不对，最好用一下）
hexo g #generator的意思，生成静态网页
hexo s #server的意思启动本地服务器
```

然后就可以访问自己的网页了

### 安装主题

```shell
hexo clean #清空缓存
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia #安装主题
```

在_config.yml文件中更改主题

```yaml
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: yilia
```

### 博客部署到服务器

在_config.yml文件中添加部署的配置信息

```yaml
deploy:
type: git
repo: git@github.com:yourname/yourname.github.io.git
branch: main #我这里改的main，原来是master，因为据说现在github主分支名字都改为main了
```

安装部署所需要的插件

```shell
npm install hexo-deployer-git --save
```

在部署之前生成mk文件，写完后，在_config.yml中设置网页标题等信息,然后直接进行部署

```shell
echo "# yourname.github.io" >> README.md#生成的文件在source\_posts中（可以不用这个命令，用下面这个命令）
hexo new post "文章名字" #建立好的文章在source\_posts中，用markdown语法编辑内容就可以了.
hexo clean
hexo g #生成网页
hexo s #本地调试
hexo d #线上部署
#如果部署不成功的话可以改为hexo d -g试试 
```

从而不需要git命令就能够完成在github服务器的部署，就可以通过在github服务器创建的网站进行查看了。

**尽量用谷歌浏览器，修改的内容百度浏览器可能显示不出来。**

## 美化配置配置

### matery主题

通过观赏别人的博客，最后感觉还是matery主题更符合我的审美

在文件夹下输入下面命令下载hexo-theme-matery

```shell
git clone https://github.com/blinkfox/hexo-theme-matery.git
```

将下载的文件夹放入themes文件文件中。然后打开_config.yml文件，将theme的值改为hexo-theme-matery。

但是主题中还有很多标签都没有填充内容，在网站中直接打开会进入404，所以根据网站把相应的东西全部配置一下。

https://github.com/blinkfox/hexo-theme-matery/blob/develop/README_CN.md

### 代码块优化

![image-20240423174229426](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240423174229426.png)

加highlight效果

![image-20240423174325617](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240423174325617.png)

由于原来的代码块太丑了，需要重新进行配置。

首先在`themes/hexo-theme-matery/source/libs/codeBlock'文件夹下创建或者修改五个`.js`文件。

```js
//codeBlockFuction.js
// 代码块功能依赖

$(function () {
    $('pre').wrap('<div class="code-area" style="position: relative"></div>');
});
```

```js
//codeBLang.js
// 代码块语言识别

$(function () {
  var $highlight_lang = $('<div class="code_lang" title="代码语言"></div>');

  $('pre').after($highlight_lang);
  $('pre').each(function () {
    var code_language = $(this).attr('class');

if (!code_language) {
  return true;
};
var lang_name = code_language.replace("line-numbers", "").trim().replace("language-", "").trim();

// 首字母大写
lang_name = lang_name.slice(0, 1).toUpperCase() + lang_name.slice(1);
$(this).siblings(".code_lang").text(lang_name);

  });
});
```

```js
//codeCopy.js
// 代码块一键复制

$(function () {
    var $copyIcon = $('<i class="fa fa-copy code_copy" title="复制代码" aria-hidden="true"></i>');
    $('.code-area').prepend($copyIcon);
new ClipboardJS('.fa-copy', {
    target: function (trigger) {
        return trigger.nextElementSibling;
    }
});

});
```

```js
//clipboard.min.js
/*!

* clipboard.js v2.0.4
* https://zenorocha.github.io/clipboard.js
* 
* Licensed MIT © Zeno Rocha
*/ 
! function (t, e) {
"object" == typeof exports && "object" == typeof module ? module.exports = e() : "function" == typeof define && define.amd ? define([], e) : "object" == typeof exports ? exports.ClipboardJS = e() : t.ClipboardJS = e()
}(this, function () {
return function (n) {
    var o = {};

function r(t) {
    if (o[t]) return o[t].exports;
    var e = o[t] = {
        i: t,
        l: !1,
        exports: {}
    };
    return n[t].call(e.exports, e, e.exports, r), e.l = !0, e.exports
}
return r.m = n, r.c = o, r.d = function (t, e, n) {
    r.o(t, e) || Object.defineProperty(t, e, {
        enumerable: !0,
        get: n
    })
}, r.r = function (t) {
    "undefined" != typeof Symbol && Symbol.toStringTag && Object.defineProperty(t, Symbol.toStringTag, {
        value: "Module"
    }), Object.defineProperty(t, "__esModule", {
        value: !0
    })
}, r.t = function (e, t) {
    if (1 & t && (e = r(e)), 8 & t) return e;
    if (4 & t && "object" == typeof e && e && e.__esModule) return e;
    var n = Object.create(null);
    if (r.r(n), Object.defineProperty(n, "default", {
            enumerable: !0,
            value: e
        }), 2 & t && "string" != typeof e)
        for (var o in e) r.d(n, o, function (t) {
            return e[t]
        }.bind(null, o));
    return n
}, r.n = function (t) {
    var e = t && t.__esModule ? function () {
        return t.default
    } : function () {
        return t
    };
    return r.d(e, "a", e), e
}, r.o = function (t, e) {
    return Object.prototype.hasOwnProperty.call(t, e)
}, r.p = "", r(r.s = 0)

}([function (t, e, n) {
    "use strict";
    var r = "function" == typeof Symbol && "symbol" == typeof Symbol.iterator ? function (t) {
            return typeof t
        } : function (t) {
            return t && "function" == typeof Symbol && t.constructor === Symbol && t !== Symbol.prototype ? "symbol" : typeof t
        },
        i = function () {
            function o(t, e) {
                for (var n = 0; n < e.length; n++) {
                    var o = e[n];
                    o.enumerable = o.enumerable || !1, o.configurable = !0, "value" in o && (o.writable = !0), Object.defineProperty(t, o.key, o)
                }
            }
            return function (t, e, n) {
                return e && o(t.prototype, e), n && o(t, n), t
            }
        }(),
        a = o(n(1)),
        c = o(n(3)),
        u = o(n(4));

function o(t) {
    return t && t.__esModule ? t : {
        default: t
    }
}
var l = function (t) {
    function o(t, e) {
        ! function (t, e) {
            if (!(t instanceof e)) throw new TypeError("Cannot call a class as a function")
        }(this, o);
        var n = function (t, e) {
            if (!t) throw new ReferenceError("this hasn't been initialised - super() hasn't been called");
            return !e || "object" != typeof e && "function" != typeof e ? t : e
        }(this, (o.__proto__ || Object.getPrototypeOf(o)).call(this));
        return n.resolveOptions(e), n.listenClick(t), n
    }
    return function (t, e) {
        if ("function" != typeof e && null !== e) throw new TypeError("Super expression must either be null or a function, not " + typeof e);
        t.prototype = Object.create(e && e.prototype, {
            constructor: {
                value: t,
                enumerable: !1,
                writable: !0,
                configurable: !0
            }
        }), e && (Object.setPrototypeOf ? Object.setPrototypeOf(t, e) : t.__proto__ = e)
    }(o, c.default), i(o, [{
        key: "resolveOptions",
        value: function () {
            var t = 0 < arguments.length && void 0 !== arguments[0] ? arguments[0] : {};
            this.action = "function" == typeof t.action ? t.action : this.defaultAction, this.target = "function" == typeof t.target ? t.target : this.defaultTarget, this.text = "function" == typeof t.text ? t.text : this.defaultText, this.container = "object" === r(t.container) ? t.container : document.body
        }
    }, {
        key: "listenClick",
        value: function (t) {
            var e = this;
            this.listener = (0, u.default)(t, "click", function (t) {
                return e.onClick(t)
            })
        }
    }, {
        key: "onClick",
        value: function (t) {
            var e = t.delegateTarget || t.currentTarget;
            this.clipboardAction && (this.clipboardAction = null), this.clipboardAction = new a.default({
                action: this.action(e),
                target: this.target(e),
                text: this.text(e),
                container: this.container,
                trigger: e,
                emitter: this
            })
        }
    }, {
        key: "defaultAction",
        value: function (t) {
            return s("action", t)
        }
    }, {
        key: "defaultTarget",
        value: function (t) {
            var e = s("target", t);
            if (e) return document.querySelector(e)
        }
    }, {
        key: "defaultText",
        value: function (t) {
            return s("text", t)
        }
    }, {
        key: "destroy",
        value: function () {
            this.listener.destroy(), this.clipboardAction && (this.clipboardAction.destroy(), this.clipboardAction = null)
        }
    }], [{
        key: "isSupported",
        value: function () {
            var t = 0 < arguments.length && void 0 !== arguments[0] ? arguments[0] : ["copy", "cut"],
                e = "string" == typeof t ? [t] : t,
                n = !!document.queryCommandSupported;
            return e.forEach(function (t) {
                n = n && !!document.queryCommandSupported(t)
            }), n
        }
    }]), o
}();

function s(t, e) {
    var n = "data-clipboard-" + t;
    if (e.hasAttribute(n)) return e.getAttribute(n)
}
t.exports = l

}, function (t, e, n) {
    "use strict";
    var o, r = "function" == typeof Symbol && "symbol" == typeof Symbol.iterator ? function (t) {
            return typeof t
        } : function (t) {
            return t && "function" == typeof Symbol && t.constructor === Symbol && t !== Symbol.prototype ? "symbol" : typeof t
        },
        i = function () {
            function o(t, e) {
                for (var n = 0; n < e.length; n++) {
                    var o = e[n];
                    o.enumerable = o.enumerable || !1, o.configurable = !0, "value" in o && (o.writable = !0), Object.defineProperty(t, o.key, o)
                }
            }
            return function (t, e, n) {
                return e && o(t.prototype, e), n && o(t, n), t
            }
        }(),
        a = n(2),
        c = (o = a) && o.__esModule ? o : {
            default: o
        };
    var u = function () {
        function e(t) {
            ! function (t, e) {
                if (!(t instanceof e)) throw new TypeError("Cannot call a class as a function")
            }(this, e), this.resolveOptions(t), this.initSelection()
        }
        return i(e, [{
            key: "resolveOptions",
            value: function () {
                var t = 0 < arguments.length && void 0 !== arguments[0] ? arguments[0] : {};
                this.action = t.action, this.container = t.container, this.emitter = t.emitter, this.target = t.target, this.text = t.text, this.trigger = t.trigger, this.selectedText = ""
            }
        }, {
            key: "initSelection",
            value: function () {
                this.text ? this.selectFake() : this.target && this.selectTarget()
            }
        }, {
            key: "selectFake",
            value: function () {
                var t = this,
                    e = "rtl" == document.documentElement.getAttribute("dir");
                this.removeFake(), this.fakeHandlerCallback = function () {
                    return t.removeFake()
                }, this.fakeHandler = this.container.addEventListener("click", this.fakeHandlerCallback) || !0, this.fakeElem = document.createElement("textarea"), this.fakeElem.style.fontSize = "12pt", this.fakeElem.style.border = "0", this.fakeElem.style.padding = "0", this.fakeElem.style.margin = "0", this.fakeElem.style.position = "absolute", this.fakeElem.style[e ? "right" : "left"] = "-9999px";
                var n = window.pageYOffset || document.documentElement.scrollTop;
                this.fakeElem.style.top = n + "px", this.fakeElem.setAttribute("readonly", ""), this.fakeElem.value = this.text, this.container.appendChild(this.fakeElem), this.selectedText = (0, c.default)(this.fakeElem), this.copyText()
            }
        }, {
            key: "removeFake",
            value: function () {
                this.fakeHandler && (this.container.removeEventListener("click", this.fakeHandlerCallback), this.fakeHandler = null, this.fakeHandlerCallback = null), this.fakeElem && (this.container.removeChild(this.fakeElem), this.fakeElem = null)
            }
        }, {
            key: "selectTarget",
            value: function () {
                this.selectedText = (0, c.default)(this.target), this.copyText()
            }
        }, {
            key: "copyText",
            value: function () {
                var e = void 0;
                try {
                    e = document.execCommand(this.action)
                } catch (t) {
                    e = !1
                }
                this.handleResult(e)
            }
        }, {
            key: "handleResult",
            value: function (t) {
                this.emitter.emit(t ? "success" : "error", {
                    action: this.action,
                    text: this.selectedText,
                    trigger: this.trigger,
                    clearSelection: this.clearSelection.bind(this)
                })
            }
        }, {
            key: "clearSelection",
            value: function () {
                this.trigger && this.trigger.focus(), window.getSelection().removeAllRanges()
            }
        }, {
            key: "destroy",
            value: function () {
                this.removeFake()
            }
        }, {
            key: "action",
            set: function () {
                var t = 0 < arguments.length && void 0 !== arguments[0] ? arguments[0] : "copy";
                if (this._action = t, "copy" !== this._action && "cut" !== this._action) throw new Error('Invalid "action" value, use either "copy" or "cut"')
            },
            get: function () {
                return this._action
            }
        }, {
            key: "target",
            set: function (t) {
                if (void 0 !== t) {
                    if (!t || "object" !== (void 0 === t ? "undefined" : r(t)) || 1 !== t.nodeType) throw new Error('Invalid "target" value, use a valid Element');
                    if ("copy" === this.action && t.hasAttribute("disabled")) throw new Error('Invalid "target" attribute. Please use "readonly" instead of "disabled" attribute');
                    if ("cut" === this.action && (t.hasAttribute("readonly") || t.hasAttribute("disabled"))) throw new Error('Invalid "target" attribute. You can\'t cut text from elements with "readonly" or "disabled" attributes');
                    this._target = t
                }
            },
            get: function () {
                return this._target
            }
        }]), e
    }();
    t.exports = u
}, function (t, e) {
    t.exports = function (t) {
        var e;
        if ("SELECT" === t.nodeName) t.focus(), e = t.value;
        else if ("INPUT" === t.nodeName || "TEXTAREA" === t.nodeName) {
            var n = t.hasAttribute("readonly");
            n || t.setAttribute("readonly", ""), t.select(), t.setSelectionRange(0, t.value.length), n || t.removeAttribute("readonly"), e = t.value
        } else {
            t.hasAttribute("contenteditable") && t.focus();
            var o = window.getSelection(),
                r = document.createRange();
            r.selectNodeContents(t), o.removeAllRanges(), o.addRange(r), e = o.toString()
        }
        return e
    }
}, function (t, e) {
    function n() {}
    n.prototype = {
        on: function (t, e, n) {
            var o = this.e || (this.e = {});
            return (o[t] || (o[t] = [])).push({
                fn: e,
                ctx: n
            }), this
        },
        once: function (t, e, n) {
            var o = this;

​    function r() {
​        o.off(t, r), e.apply(n, arguments)
​    }
​    return r._ = e, this.on(t, r, n)
},
emit: function (t) {
​    for (var e = [].slice.call(arguments, 1), n = ((this.e || (this.e = {}))[t] || []).slice(), o = 0, r = n.length; o < r; o++) n[o].fn.apply(n[o].ctx, e);
​    return this
},
off: function (t, e) {
​    var n = this.e || (this.e = {}),
​        o = n[t],
​        r = [];
​    if (o && e)
​        for (var i = 0, a = o.length; i < a; i++) o[i].fn !== e && o[i].fn._ !== e && r.push(o[i]);
​    return r.length ? n[t] = r : delete n[t], this
}

}, t.exports = n

}, function (t, e, n) {
    var d = n(5),
        h = n(6);
    t.exports = function (t, e, n) {
        if (!t && !e && !n) throw new Error("Missing required arguments");
        if (!d.string(e)) throw new TypeError("Second argument must be a String");
        if (!d.fn(n)) throw new TypeError("Third argument must be a Function");
        if (d.node(t)) return s = e, f = n, (l = t).addEventListener(s, f), {
            destroy: function () {
                l.removeEventListener(s, f)
            }
        };
        if (d.nodeList(t)) return a = t, c = e, u = n, Array.prototype.forEach.call(a, function (t) {
            t.addEventListener(c, u)
        }), {
            destroy: function () {
                Array.prototype.forEach.call(a, function (t) {
                    t.removeEventListener(c, u)
                })
            }
        };
        if (d.string(t)) return o = t, r = e, i = n, h(document.body, o, r, i);
        throw new TypeError("First argument must be a String, HTMLElement, HTMLCollection, or NodeList");
        var o, r, i, a, c, u, l, s, f
    }
}, function (t, n) {
    n.node = function (t) {
        return void 0 !== t && t instanceof HTMLElement && 1 === t.nodeType
    }, n.nodeList = function (t) {
        var e = Object.prototype.toString.call(t);
        return void 0 !== t && ("[object NodeList]" === e || "[object HTMLCollection]" === e) && "length" in t && (0 === t.length || n.node(t[0]))
    }, n.string = function (t) {
        return "string" == typeof t || t instanceof String
    }, n.fn = function (t) {
        return "[object Function]" === Object.prototype.toString.call(t)
    }
}, function (t, e, n) {
    var a = n(7);

function i(t, e, n, o, r) {
    var i = function (e, n, t, o) {
        return function (t) {
            t.delegateTarget = a(t.target, n), t.delegateTarget && o.call(e, t)
        }
    }.apply(this, arguments);
    return t.addEventListener(n, i, r), {
        destroy: function () {
            t.removeEventListener(n, i, r)
        }
    }
}
t.exports = function (t, e, n, o, r) {
    return "function" == typeof t.addEventListener ? i.apply(null, arguments) : "function" == typeof n ? i.bind(null, document).apply(null, arguments) : ("string" == typeof t && (t = document.querySelectorAll(t)), Array.prototype.map.call(t, function (t) {
        return i(t, e, n, o, r)
    }))
}

}, function (t, e) {
    if ("undefined" != typeof Element && !Element.prototype.matches) {
        var n = Element.prototype;
        n.matches = n.matchesSelector || n.mozMatchesSelector || n.msMatchesSelector || n.oMatchesSelector || n.webkitMatchesSelector
    }
    t.exports = function (t, e) {
        for (; t && 9 !== t.nodeType;) {
            if ("function" == typeof t.matches && t.matches(e)) return t;
            t = t.parentNode
        }
    }
}])
});
```

```
//codeShrink.js
// 代码块收缩

$(function () {
  var $code_expand = $('<i class="fa fa-chevron-down code-expand" title="折叠代码" aria-hidden="true"></i>');

  $('.code-area').prepend($code_expand);
  $('.code-expand').on('click', function () {
    if ($(this).parent().hasClass('code-closed')) {
      $(this).siblings('pre').find('code').show();
      $(this).parent().removeClass('code-closed');
    } else {
      $(this).siblings('pre').find('code').hide();
      $(this).parent().addClass('code-closed');
    }
  });
});
```

在`themes/hexo-theme-matery/source/css/matery.css`文件下添加

```css
code {
    padding: 1px 5px;
    font-family: Inconsolata, Monaco, Consolas, 'Courier New', Courier, monospace;
    /* font-size: 0.91rem; */
    color: #e96900;
    background-color: #f8f8f8;
    border-radius: 2px;
}

pre code {
    padding: 0;
    color: #e8eaf6;
    background-color: #272822;
}

pre[class*="language-"] {
    padding: 1.2em;
    margin: .5em 0;
}

code[class*="language-"],
pre[class*="language-"] {
    color: #e8eaf6;
    white-space: pre-wrap !important;
} */

.line-numbers-rows {
    border-right-width: 0px !important;
}

.line-numbers {
    padding: 1.5rem 1.5rem 1.5rem 3.2rem !important;
    margin: 1rem 0 !important;
    background: #272822;
    overflow: auto;
    border-radius: 0.35rem;
    tab-size: 4;
}


pre {
    padding: 1.5rem !important;
    margin: 1rem 0 !important;
    background: #272822;
    overflow: auto;
    border-radius: 0.35rem;
    tab-size: 4;
}

pre::before {
    content: "";
    height: 16px;
    margin-bottom: 0;
    display: block;
}

pre::after {
    content: " ";
    position: absolute;
    border-radius: 50%;
    background: #ff5f56;
    width: 12px;
    height: 12px;
    top: 0;
    left: 12px;
    margin-top: 12px;
    -webkit-box-shadow: 20px 0 #ffbd2e, 40px 0 #27c93f;
    box-shadow: 20px 0 #ffbd2e, 40px 0 #27c93f;
}

code {
    padding: 1px 5px;
    font-family: Inconsolata, Monaco, Consolas, 'Courier New', Courier, monospace;
    font-size: 0.91rem;
    color: #e96900;
    background-color: #f8f8f8;
    border-radius: 2px;
}

.code_copy {
    position: absolute;
    top: 0.7rem;
    right: 35px;
    z-index: 1;
    filter: invert(50%);
    cursor: pointer;
}

.code_lang {
    position: absolute;
    top: 1.2rem;
    right: 60px;
    line-height: 0;
    font-weight: bold;
    font-family: normal;
    z-index: 1;
    filter: invert(50%);
    cursor: pointer;
} 

.code-expand {
    position: absolute;
    top: 4px;
    right: 0px;
    filter: invert(50%);
    padding: 7px 10px;
    z-index: 1;
    cursor: pointer;
    transition: all .3s;
    transform: rotate(0deg);
}

.code-closed .code-expand {
    transform: rotate(-180deg) !important;
    transition: all .3s;
}

.code-closed pre::before {
    height: 0px;
}

pre code {
    padding: 0;
    color: #e8eaf6;
    background-color: #272822;
}

pre[class*="language-"] {
    padding: 1.2em;
    margin: .5em 0;
}

code[class*="language-"],
pre[class*="language-"] {
    color: #e8eaf6;
    white-space: pre-wrap !important;
}
```

最后在`themes\hexo-theme-matery\layout\post.ejs`中添加代码

```
<script type="text/javascript" src="/libs/codeBlock/codeBlockFuction.js"></script>
<!-- 代码语言 -->
<script type="text/javascript" src="/libs/codeBlock/codeLang.js"></script>
<!-- 代码块复制 -->
<script type="text/javascript" src="/libs/codeBlock/codeCopy.js"></script>
<script type="text/javascript" src="/libs/codeBlock/clipboard.min.js"></script>
<!-- 代码块收缩 -->
<script type="text/javascript" src="/libs/codeBlock/codeShrink.js"></script> 
<!-- 代码块折行 -->
<style type="text/css">code[class*="language-"], pre[class*="language-"] { white-space: pre !important; }</style>
```

### 通过插件美化

#### 代码块渲染

上面直接写js,css的方法过于硬核，一般还是采用安装插件来实现渲染。一般采用prism完成代码语法高亮。

##### 首先下载Prism配置文件：[Download ▲ Prism (prismjs.com)](https://prismjs.com/download.html#themes=prism&languages=markup+css+clike+javascript+abap+abnf+actionscript+ada+agda+al+antlr4+apacheconf+apex+apl+applescript+aql+arduino+arff+armasm+arturo+asciidoc+aspnet+asm6502+asmatmel+autohotkey+autoit+avisynth+avro-idl+awk+bash+basic+batch+bbcode+bbj+bicep+birb+bison+bnf+bqn+brainfuck+brightscript+bro+bsl+c+csharp+cpp+cfscript+chaiscript+cil+cilkc+cilkcpp+clojure+cmake+cobol+coffeescript+concurnas+csp+cooklang+coq+crystal+css-extras+csv+cue+cypher+d+dart+dataweave+dax+dhall+diff+django+dns-zone-file+docker+dot+ebnf+editorconfig+eiffel+ejs+elixir+elm+etlua+erb+erlang+excel-formula+fsharp+factor+false+firestore-security-rules+flow+fortran+ftl+gml+gap+gcode+gdscript+gedcom+gettext+gherkin+git+glsl+gn+linker-script+go+go-module+gradle+graphql+groovy+haml+handlebars+haskell+haxe+hcl+hlsl+hoon+http+hpkp+hsts+ichigojam+icon+icu-message-format+idris+ignore+inform7+ini+io+j+java+javadoc+javadoclike+javastacktrace+jexl+jolie+jq+jsdoc+js-extras+json+json5+jsonp+jsstacktrace+js-templates+julia+keepalived+keyman+kotlin+kumir+kusto+latex+latte+less+lilypond+liquid+lisp+livescript+llvm+log+lolcode+lua+magma+makefile+markdown+markup-templating+mata+matlab+maxscript+mel+mermaid+metafont+mizar+mongodb+monkey+moonscript+n1ql+n4js+nand2tetris-hdl+naniscript+nasm+neon+nevod+nginx+nim+nix+nsis+objectivec+ocaml+odin+opencl+openqasm+oz+parigp+parser+pascal+pascaligo+psl+pcaxis+peoplecode+perl+php+phpdoc+php-extras+plant-uml+plsql+powerquery+powershell+processing+prolog+promql+properties+protobuf+pug+puppet+pure+purebasic+purescript+python+qsharp+q+qml+qore+r+racket+cshtml+jsx+tsx+reason+regex+rego+renpy+rescript+rest+rip+roboconf+robotframework+ruby+rust+sas+sass+scss+scala+scheme+shell-session+smali+smalltalk+smarty+sml+solidity+solution-file+soy+sparql+splunk-spl+sqf+sql+squirrel+stan+stata+iecst+stylus+supercollider+swift+systemd+t4-templating+t4-cs+t4-vb+tap+tcl+tt2+textile+toml+tremor+turtle+twig+typescript+typoscript+unrealscript+uorazor+uri+v+vala+vbnet+velocity+verilog+vhdl+vim+visual-basic+warpscript+wasm+web-idl+wgsl+wiki+wolfram+wren+xeora+xml-doc+xojo+xquery+yaml+yang+zig&plugins=line-numbers+show-language+inline-color+toolbar+copy-to-clipboard)

- 选择主题
- 选择语言（可以全选）
- 选择插件（Line Numbers：行号显示，Show Language：语言显示，Inline color：颜色预览，Copy to Clipboard Button：复制按钮）

##### 更改hexo本地配置

- 在themes\hexo-theme-matery\source\js下新建一个prism文件夹，将下载的js和css文件复制过去。
- 打开themes\hexo-theme-matery\layout\_partial文件夹，打开head.ejs，在最后加一行代码。

```ejs
<head>
  ...
  <link rel="stylesheet" href="/js/prism/prism.css">
</head>
```

- 打开footer.ejs，加一行代码

```ejs
<footer class="footer">
  ...
  <script src="/js/prism/prism.js" async></script>
</footer>
```

- 行号插件需要单独配置，在\themes\hexo-theme-matery\layout\layout.ejs中加入下列代码

```ejs
<body class="line-numbers">
  ...
</body>
```

- 最后更改主题配置文件_config.yml，将内置的一些功能关闭，highlighter选择prismjs

```yaml
syntax_highlighter: prismjs
prismjs:
  preprocess: true
  line_number: true
  line_threshold: 0
  tab_replace: ''
```



##### 卸载原有插件

```bash
npm uninstall hexo-prism-plugin
```

### 公式渲染

主题原有的hexo-renderer-marked并不支持latex公式渲染，所以改为hexo-renderer-markdown-it-plus，同时安装hexo-math。命令如下

```bash
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-markdown-it-plus --save
npm install hexo-math --save
```

公式渲染结果如下
$$
\epsilon xafg=1564
$$
