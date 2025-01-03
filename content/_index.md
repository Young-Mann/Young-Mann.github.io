---
display_section: posts
---

<style>
    p {    
         text-indent: 2em;
         text-justify: inter-ideograph;
         text-align: left-align;
         font-size: 0.875em;
    }
    .tomb img {
        cursor: pointer;
        transition: opacity 0.5s ease, transform 0.5s ease;
    }
    .tomb figcaption {
        transition: opacity 0.5s ease, transform 0.5s ease;
    }
</style>

<span class="tomb">
<figure>
  <img id="switchImage" src="https://s2.loli.net/2024/09/24/loMhr7yZzc54uXm.jpg" width="70%">
  <figcaption id="caption">堆于 2023.11.30<br>我的学生卡长眠于此</figcaption>
</figure>

你好呀，这里是 [我](../posts/about) 的文字坟头，存放的多是从课本上抄来的内容，亦或是我线下生活的流水账。读起来应该会挺无趣，从这些 [标签](../tags) 可窥见一斑。

若是觉得晦气，想要和我“友好”互动，那欢迎 [发邮件至此](mailto:young-mann1043@outlook.com) 。让咱俩好好比划比划！

</span>

<script>
    const slides = [
        {
            src: "https://s2.loli.net/2024/09/24/loMhr7yZzc54uXm.jpg",
            caption: "堆于 2023.11.30<br>我的学生卡长眠于此"
        },
        {
            src: "https://s2.loli.net/2025/01/03/QFPstSp7AXyG4lB.jpg",
            caption: "生于 2024.11.22<br>无声而立，卒时不记"
        }
    ];
    
    let currentIndex = 0;
    const imgElement = document.getElementById("switchImage");
    const captionElement = document.getElementById("caption");

    imgElement.onclick = function() {
        // 淡出并缩小
        imgElement.style.opacity = 0;
        imgElement.style.transform = "scale(0.9)";
        captionElement.style.opacity = 0;
        captionElement.style.transform = "scale(0.9)";

        // 等待动画完成后切换图片和文字
    setTimeout(() => {
        currentIndex = (currentIndex + 1) % slides.length;
        imgElement.src = slides[currentIndex].src;
        captionElement.innerHTML = slides[currentIndex].caption;  // 使用innerHTML

        // 淡入并放大
        imgElement.style.opacity = 1;
        imgElement.style.transform = "scale(1)";
        captionElement.style.opacity = 1;
        captionElement.style.transform = "scale(1)";
    }, 500);

    }
</script>
