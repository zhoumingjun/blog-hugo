+++
date = "2017-03-10T11:40:12+08:00"
title = "Using MathJax and Graphviz with Hugo"
Categories = ["blog"]
Tags = ["tricks"]
Description = "Using MathJax and Graphviz with Hugo"
menu = ""
math = true
viz = true
+++

# MathJax support
## configuration
[Hugo doc](https://gohugo.io/tutorials/mathjax/)    
[MathJax doc](http://docs.mathjax.org/en/latest/configuration.html?#using-plain-javascript)     

``` 
<!-- LaTeX math rendering -->
{{ if  .Params.math   }}
<script type="text/javascript">
    window.MathJax = {
        tex2jax: {
            inlineMath: [['$','$'], ['\\(','\\)']],
            displayMath: [['$$','$$'], ['\[','\]']],
            processEscapes: true,
            processEnvironments: true,
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            TeX: { equationNumbers: { autoNumber: "AMS" },
                extensions: ["AMSmath.js", "AMSsymbols.js", "color.js"] }
        },
        AuthorInit: function () {
            MathJax.Hub.Register.StartupHook("Begin",function () {
                MathJax.Hub.Queue(function() {
                    var all = MathJax.Hub.getAllJax(), i;
                    for(i = 0; i < all.length; i += 1) {
                        all[i].SourceElement().parentNode.className += ' has-jax';
                    }
                })
            });
        }
    };
</script>
<script  type="text/javascript"
    src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
{{ end }}
```

## example 
**code**    
```
    `$ v_\pi(s) = \sum_{a \in \mathcal{A}} \pi(a|s) q_\pi(s,a)  $`
```
**result**  
`$ v_\pi(s) = \sum_{a \in \mathcal{A}} \pi(a|s) q_\pi(s,a)  $`

# graphviz support

## configuration
```html 
<!-- graphviz renderring -->
{{ if  .Params.viz}}
<script type="text/javascript" src="//cdnjs.cloudflare.com/ajax/libs/viz.js/1.7.1/viz.js"> </script>
<script type="text/javascript">
(function(){
    var vizPrefix = "language-viz-";
    Array.prototype.forEach.call(document.querySelectorAll("[class^=" + vizPrefix + "]"), function(x){
        var engine;
        x.getAttribute("class").split(" ").forEach(function(cls){
            if (cls.startsWith(vizPrefix)) {
                engine = cls.substr(vizPrefix.length);
            }
        });
        var image = new DOMParser().parseFromString(Viz(x.innerText, {format:"svg", engine:engine}), "image/svg+xml");
        x.parentNode.insertBefore(image.documentElement, x);
        x.style.display = 'none'
    });
})();
</script>
{{ end }}
```

## example
**code**        
```
    ```viz-dot
    digraph g { 
    node[shape="circle" , label="", width=0.2, height=0.2]
    l1[xlabel="v\(s\)"]
    l21[xlabel="a", width=0.1, height=0.1 , style=filled]
    l22[width=0.1, height=0.1, style=filled]
    l31[xlabel="v\(s'\)"]

    l1 -> l21
    l1 -> l22
    l21 -> l31 [xlabel="r"]
    l21 -> l32
    l22 -> l33
    l22 -> l34
    }
    ```
```
**result**   
```viz-dot
digraph g { 
   node[shape="circle" , label="", width=0.2, height=0.2]
   l1[xlabel="v\(s\)"]
   l21[xlabel="a", width=0.1, height=0.1 , style=filled]
   l22[width=0.1, height=0.1, style=filled]
   l31[xlabel="v\(s'\)"]

   l1 -> l21
   l1 -> l22
   l21 -> l31 [xlabel="r"]
   l21 -> l32
   l22 -> l33
   l22 -> l34
}
```