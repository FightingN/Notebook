[TOC]

# 1.防抖和节流

- debounce函数只会被调用一次，返回一个函数。而事件触发调用的只是返回的那个函数

## 1.1 防抖

-   定义
    - 在事件被触发 n 秒后再执行回调，如果在这 n 秒内事件又被触发，则重新计时。这可以使用在一些点击请求的事件上，避免因为用户的多次点击向后端发送多次请求。
-   使用场景
  - search搜索联想，用户在不断输入值时，用防抖来节约请求资源。
  - window触发resize的时候，不断的调整浏览器窗口大小会不断的触发这个事件，用防抖来让其只触发一次

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="utf-8">
    <title>防抖与节流</title>
    <script type="text/javascript">
        /**
         * @desc 函数防抖
         * @param func 函数
         * @param wait 延迟执行毫秒数
         * @param immediate true 表立即执行，false 表非立即执行
         */
        function debounce(func, wait, immediate) {
            let timeout;
            return function () {
                let context = this;
                let args = arguments;

                if (timeout) clearTimeout(timeout);
                if (immediate) {
                    var callNow = !timeout;
                    timeout = setTimeout(() => {
                        timeout = null;
                    }, wait)
                    if (callNow) func.apply(context, args)
                }
                else {
                    timeout = setTimeout(function () {
                        func.apply(context, args)
                    }, wait);
                }
            }
        }
    </script>
</head>

<body>
    <div id="app" style="height:3000px;width: 100%;"></div>
    <script>
        document.body.onscroll = debounce(function () {
            console.log('onscroll');
        }, 200, true)
    </script>
</body>

</html>

```

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<style>
    div {
        width: 100%;
        height: 300px;
        background: #ccc;
    }
</style>

<body>
    <div id="app"></div>
    <span id="button">按钮</span>
</body>
<script>
    function debounce(func, wait, immediate) {
        let timeout, result;
        let debounced = function () {
            let context = this
            let args = arguments
            if (timeout) {
                clearTimeout(timeout)
            }
            if (immediate) {
                console.log('布尔', Boolean(timeout))
                let callNow = !timeout
                timeout = setTimeout(function () {
                    timeout = null
                }, wait)
                if (callNow) {
                    result = func.apply(context, args)
                }
            } else {
                timeout = setTimeout(function () {
                    result = func.apply(context, args)
                }, wait)
            }
            return result
        }
        debounced.cancel = function () {
            clearTimeout(timeout);
            timeout = null
        }
        return debounced

    }

    let div = document.getElementById("app")
    let button = document.getElementById("button")
    function doSomething() {
        console.log('onclick')
        return console.log('eeee')
    }
    let dosome = debounce(doSomething, 3000)
    div.onclick = dosome
    button.onclick = function () {
        // 取消防抖
        dosome.cancel()
    }
</script>

</html>
```

- 优化:在函数内部加了返回值和取消防抖的方法

## 1.2节流

- 定义
  - 连续触发事件在 n 秒内只执行一次函数。节流会稀释函数的执行频率。
  - 指规定一个单位时间，在这个单位时间内，只能有一次触发事件的回调函数执行，如果在同一个单位时间内某事件被触发多次，只有一次能生效。节流可以使用在 scroll 函数的事件监听上，通过事件节流来降低事件调用的频率。、

- 使用场景

  -  鼠标不断点击触发，mousedown(单位时间内只触发一次)

  -  监听滚动事件，比如是否滑到底部自动加载更多，用throttle来判断
  - 页面resize事件，常见于需要做页面适配的时候。需要根据最终呈现的页面情况进行dom渲染（这种情形一般是使用防抖，因为只需要判断最后一次的变化情况）

  ​	

```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="utf-8">
		<title>防抖与节流</title>
		<script type="text/javascript">
			/**
			 * @function: throttle
			 * @param: {Function} func 要执行的函数
			 * @description: 当函数在短时间内多次触发时，做节流，间隔delay时长再去执行
			 * @author: hruomei
			 * @date: 2020-10-29 10:38:06
			 * @version: V1.0.0
			 */
			function throttle(func, delay) {
				let timer = null, // 用来保存setTimeout返回的值
					startTime = Date.now(); // 创建节流函数的时间
			
			    // 返回一个函数
				return function() {
					let curTime = Date.now(), // 返回的这个函数被调用的时间
						remaining = delay - (curTime - startTime), // 设定的delay与[上一次被调用的时间与现在的时间间隔]的差值
						context = this, // 上下文对象
						args = arguments; // 返回的这个函数执行时传入的参数
			
			        // 首先清掉定时器
					clearTimeout(timer); 
			
			        // // 假如距离上一次执行此函数的时间已经超过了设定的delay，则执行
					if (remaining <= 0) { 
						func.apply(context, args);
						startTime = Date.now(); // 重置最后执行时间为现在
			
			        // 否则，等到间隔时间达到delay时，执行函数
					} else {
						timer = setTimeout(() => {
			                func.apply(context, args);
			            }, remaining);
					}
				}
			}
		</script>
	</head>
	<body>
		<div id="app" style="height:3000px;width: 100%;"></div>
		<script>
		    document.body.onscroll = throttle(function() {
				console.log('onscroll');
			}, 200)
		</script>
	</body>
</html>
```



