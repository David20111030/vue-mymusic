
#使用Vue2+webpack+Es6快速开发一个移动端项目（附详细代码解析）

### 最近看到不少使用vue制作的音乐播放器，挺好玩的，本来工作中也经常使用Vue
### 本项目特点如下 ： 
    1. 原生js封装自己的跨域请求函数，支持promise调用，支持错误处理
    2. 制作一些复用性强的vue组件，如轮播图组件，支持手势滑动，无限循环，图片按需加载
    3. 清晰明了的项目目录
[目录](https://github.com/derickweng/vue-mymusic/blob/master/imgforread/xiang.png) 
[演示](https://github.com/derickweng/vue-mymusic/blob/master/imgforread/test.gif)
 手写跨域调用：（services-index.js）
 ```javascript
    function deal (res) {//接口返回code码处理
        if (res.code && res.code != 0) {
            return Promise.reject(res)
        }
        return Promise.resolve(res)
    }
    function myJsonp(url,params){  //封装jsonp跨域请求,采用promis形式封装
    return new Promise ((resolve,reject) => {
        try {
            let scriptEle = document.createElement('script'),
                headEle = document.getElementsByTagName('head')[0],
                srcPar = '',
                time = new Date().getTime()+'_'+Math.random().toString().substr(2); //直接使用时间戳还是有产生相同的函数名，使用随机数减小概率，
            Object.keys(params).forEach((item) => {
                if (item != 'callback') {
                    srcPar += `${item}=${params[item]}&`
                }
            })   
            scriptEle.src = `${url}?${srcPar}${params.callback}=jsonpback${time}`
            headEle.appendChild(scriptEle)
            window[`jsonpback${time}`] = function(result){
                resolve(result)
                headEle.removeChild(scriptEle)//用完后清除js
            }
        }catch(e){
             reject(e)
            }
        })
    }

    function jsonp(url,params) {
        return myJsonp(url,params).then(res=>{
            return deal(res)
        })
    }
    export default {
        jsonp
    }
 ```
