# 消息滚动，实现首尾相接循环滚动的效果
```
  <ul>
    <li>简单</li>
    <li>清新</li>
    <li>快速</li>
    <li>未来</li>
  </ul>
```
两种实现方式：
* 复制一份ul，第一个ul滚出视觉范围时重新定位到下方，接在第二个ul下方，这样不断循环。
```
  <ul id="noticeMain">
    <li>简单</li>
    <li>清新</li>
    <li>快速</li>
    <li>未来</li>
  </ul>
  <ul id="noticeMain2">
    <li>简单</li>
    <li>清新</li>
    <li>快速</li>
    <li>未来</li>
  </ul>
  
  js:
    //消息轮播
    var setNoticeSlide = function(res){
        noticeMain.append(tmplNotice(res));
        noticeMain2.append(tmplNotice(res));
        dataSrc(noticeMain);
        dataSrc(noticeMain2);

        var len = res.data.length;
        var liHeight = $(noticeMain.find('li')[0]).height();
        var wholeHeight = liHeight * len;
        var boxWidth = parseInt(noticeMain.height()); // 显示的容器宽度
        var x = 0;
        var y = wholeHeight;
        noticeMain.css({"height": wholeHeight + "px", "top": x + "px"}); // 根据li的实际占宽，动态写入ul的宽度
        noticeMain2.css({"height": wholeHeight + "px", "top": y + "px" }); // 第二个ul放在第一个ul的下边

        function circleScroll() {
            if(x == -wholeHeight) {    //第一个ul刚好滚出容器
                x = wholeHeight - liHeight; //把第一个ul放到下边
                noticeMain.css({"top": x + "px"});
                y -= liHeight;
                noticeMain2.animate({
                    top: y
                },1000,'linear');

            }else if(y == -wholeHeight) {   //第二个ul刚好滚出容器
                 y = wholeHeight - liHeight;
                 noticeMain2.css({"top": y + "px"});
                 x -= liHeight;
                 noticeMain.animate({
                     top: x
                 },1000,'linear');
            }else {
                x -= liHeight;
                y -= liHeight;
                noticeMain.animate({
                    top: x
                },1000,'linear');
                noticeMain2.animate({
                    top: y
                },1000,'linear');

            }
        };
        var timer = setInterval( function() {
            circleScroll();
        }, 1700 ); // 循环滚动

    };
```
* 所有的li都首先translateY(100%)到容器下方，重叠在一起，按顺序出现在容器内，再向上translateY(-100%)滚出容器，只多复制第一个li放在末尾，作为一轮滚动完的过渡
```
  <!DOCTYPE html>
<html>
<meta charset="utf-8">
<head>
    <title>test scroll</title>
    <style type="text/css">
        * {
            margin: 0;
            padding: 0;
        }
        body,html {
            height: 100%;
        }
        h1 {
            font-size: 60px;
        }
        .text-mask {
            position: relative;
            display: block;
            width: 100%;
            overflow: hidden;
            height: 60px;
            text-align: center;
            margin-top: 100px;
        }
        .hero-title-carousel {
            display: block;
            position: relative;
            width: 100%;
            list-style: none;
            margin-left: 0;
        }
        [class*=cubic-easeinout] {
            transition: opacity cubic-bezier(.19,1,.22,1),background-color cubic-bezier(.19,1,.22,1),-webkit-transform cubic-bezier(.19,1,.22,1);
            transition: transform cubic-bezier(.19,1,.22,1),opacity cubic-bezier(.19,1,.22,1),background-color cubic-bezier(.19,1,.22,1);
            transition: transform cubic-bezier(.19,1,.22,1),opacity cubic-bezier(.19,1,.22,1),background-color cubic-bezier(.19,1,.22,1),-webkit-transform cubic-bezier(.19,1,.22,1);
        }
        [class*="--fast"] {
            transition-duration: 1s,1s,1s;
        }
        .hero-title-carousel li {
            display: block;
            position: absolute;
            width: 100%;
        }
        .hero-title-carousel-hide {
            opacity: 0!important;
            -webkit-transform: translateY(-100%) translateZ(0)!important;
            transform: translateY(-100%) translateZ(0)!important;
        }
        .hero-title-carousel-reveal {
            -webkit-transform: translateY(100%) translateZ(0);
            transform: translateY(100%) translateZ(0);
        }
    </style>
</head>
<body>
    <div class="wrap">
        <span class="text-mask">
        <ul id="carousel-first" class="hero-title-carousel">

          <li class="cubic-easeinout--fast">
              <h1>快速。</h1>
          </li>

          <li class="cubic-easeinout--fast hero-title-carousel-reveal">
              <h1>简单。</h1>
          </li>

          <li class="cubic-easeinout--fast hero-title-carousel-reveal">
              <h1>有效。</h1>
          </li>

          <li class="cubic-easeinout--fast hero-title-carousel-reveal">
              <h1>安全。</h1>
          </li>

          <li class="cubic-easeinout--fast hero-title-carousel-reveal">
              <h1>未来。</h1>
          </li>

          <li class="cubic-easeinout--fast hero-title-carousel-reveal">
              <h1>快速。</h1>
          </li>

        </ul>
        </span>
    </div>
    <script type="text/javascript" src="http://code.jquery.com/jquery-1.8.0.min.js"></script>
    <script type="text/javascript">
    var scroll = function() {
        var t=$("#carousel-first").find(".hero-title-carousel-reveal");
        if(0===t.length){
            var e=$("#carousel-first li");
            e.removeClass("cubic-easeinout--fast");
            e.addClass("hero-title-carousel-reveal").removeClass("hero-title-carousel-hide");
            $(e[0]).removeClass("hero-title-carousel-reveal");
            setTimeout(function(){
                e.addClass("cubic-easeinout--fast");
                $(e[0]).addClass("hero-title-carousel-hide");
                $(e[0]).next().removeClass("hero-title-carousel-reveal");
            },100)
        }
        var t=$(t[0]);t.prev().addClass("hero-title-carousel-hide");
        t.removeClass("hero-title-carousel-reveal");
    }
    setInterval(function(){
       scroll();
    },1500);

    </script>

```
