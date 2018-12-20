# 遇到的angular与vue混写问题 #
## 2018.12.20 ##
写请求后台数据的时候遇到的问题，请求方式很奇怪，一开始以为是vue的某种写法，尝试$ajax（）重写，但是逻辑一直对不上，后期直接改掉请求路径，请求一个本地的json文件，火狐成功运行，但是有跨域问题..？谷歌无法请求成功，

最后发现
>  this.$http.get().then（function())

好像为angular代码。。

谷歌跨域报错代码：

![](https://i.imgur.com/LAtXlGb.png)

火狐虽然运行成功但会报json的错
![](https://i.imgur.com/1WAM0k3.png)


----------------------------------------分割线

最后附上代码和数据
    
	var dir=0,der=1;
		var app=new Vue({
    el:'#app'
    ,data:{
        inData:{}
        ,RenderingData:{
            exihibitpic:''
        }//最终渲染数据
        ,documentTypeList:{
            medical_report:false//影像报告，出入院报告
            ,medical_record_home:false//病案首页
            ,cost_listing:false//费用清单
            ,invoice:false//发票
            ,laboratory:false//化验单
            ,pres:false//处方单
            ,other:false//未知
        }
        ,Flag:[]
    }
    ,created:function () {
        console.log('created');
        var _this=this;
        // var srceach_details=JSON.parse(localStorage.srceach_details)
        var url='./json.json';
        // var url='/api/ocr/validation/the_case_all/?business_id='+srceach_details.business_id+'&user_code='+localStorage.user_code;
        this.$http.get(url).then(function (data) {
            var DataMsg=data.data.data;
            for(i in DataMsg){
                if(DataMsg[i].length===0){
                    continue
                }
                _this.documentTypeList[i]=true;
                _this.RenderingData=DataMsg[i][0];
                switch (i){
                    case 'invoice':
                        _this.flagCutting(_this.RenderingData.flag);
                        break;
                    case 'cost_listing':
                        for (var i=0;i<_this.RenderingData.cos_project_list.length;i++){
                            _this.Flag.push(_this.RenderingData.cos_project_list[i].project_check_str.split('')[6]);
                        }
                        break;
                }
                break;
            }
            if(data.data.ret_cd!==200){
                alert('请求出错，代码：'+data.data.ret_cd)
                return;
            }
            _this.inData=data.data.data;
        },function (err) {
            console.log(err);
        })
    }
    ,mounted:function (){
        console.log('mounted');
    }
    ,methods: {
        init: function () {
        }
        ,flagCutting:function (flag) {
            this.Flag=flag.split('');
            return true
        }
        // 旋转效果
        ,direction:function (data) {
            var ImaGes=document.getElementsByClassName('imgButBox')[0];
            console.log(ImaGes);
            var wid=ImaGes.offsetHeight;
            var hei=ImaGes.offsetWidth;
            if(data){
                dir-=90;
                ImaGes.style.transition='ease .5s';
                if(!((dir / 90)%2===0)){
                    ImaGes.style.width='100%';
                }
                ImaGes.style.transform='rotate('+dir+'deg) scale('+der+')';
            }else{
                dir+=90;
                ImaGes.style.transition='ease .5s';
                if(!((dir / 90)%2===0)){
                    ImaGes.style.width='100%';
                }
                ImaGes.style.transform='rotate('+dir+'deg) scale('+der+')';
            }
        }
        //点击列表
        ,ListClick:function (Type,data) {
            var _this=this;
            for(var i in _this.documentTypeList){
                _this.documentTypeList[i]=false
            }
            _this.documentTypeList[Type]=true;
            _this.RenderingData=data;
            console.log(data);
            if (Type==='invoice'){
                _this.flagCutting(data.flag);
            }
        }
        // 放大效果
        ,enlarge:function (data) {
            var ImaGes=document.getElementsByClassName('imgButBox')[0];
            if(data){
                der+=0.3;
                if(der>3){der=3}
                ImaGes.style.transition='ease .5s';
                ImaGes.style.transform='rotate('+dir+'deg) scale('+der+')';
            }else{
                der-=0.3;
                if(der<0.3){der=0.3}
                ImaGes.style.transition='ease .5s';
                ImaGes.style.transform='rotate('+dir+'deg) scale('+der+')';
            }
        }
        //鼠标 滑过 按钮组 效果
        ,suspensionTitle_block:function (e,bur,data,s) {
            var dom=document.getElementById('suspensionTitle');
            if(e==='mot'){
                dom.style.display='block';
                if(bur===false){
                    dom.style.display='none';
                }
            }
            if(data===undefined){
                return
            }
            if(!bur){
                dom.style.display='none';
            }
            dom.firstElementChild.innerText='姓名：'+data+',医院：'+s;
            dom.style.left=e.clientX+'px';
            dom.style.top=e.clientY+'px';
            dom.style.display='block';
        }
        //删除
        ,dataDel:function (data) {
            console.log(data);
        }
    }
	});
	app.init();
	//拖拽效果
	function mos(event) {
    var movDom=document.getElementsByClassName('imgButBox')[0]
        ,_thisDom=event.path[0];
    var disL=movDom.offsetLeft,disT=movDom.offsetTop,X=event.clientX,Y=event.clientY;
    movDom.style.transition='';
    document.onmousemove = function (eve) {
        movDom.style.left=disL+eve.clientX-X+'px';
        movDom.style.top=disT+eve.clientY-Y+'px';
    };
    document.onmouseup = function (eve) {
        document.onmousemove = function (eve) {
            return false;
        };
    };
    // document.onmouseout = function (eve) {
    //     document.onmousemove = function (eve) {
    //         return false;
    //     };
    // }
	}


模拟数据

    	{
	"ret_ts": 1545295289,
	"case_id": "ososooso",
	"data": {
		"medical_report": [],
		"medical_record_home": [],
		"other": [],
		"cost_listing": [],
		"invoice": [],
		"laboratory": [{
			"\u56fe\u7247\u5730\u5740": "http://182.92.106.16:8022/images/exhibited_picture/shumei_1545018954_7751_docx.jpg",
			"\u59d3\u540d": "",
			"\u8bca\u65ad": "",
			"indicators": [{
				"index_flag": "0",
				"index_value": "37.5",
				"index_normal_value": "0.1-50",
				"index_name": "\u5927\u578b\u8840\u5c0f\u677f\u6bd4\u7387",
				"index_unit": "%",
				"index_id": 22703
			}, {
				"index_flag": "0",
				"index_value": "12.910",
				"index_normal_value": "",
				"index_name": "\u7ea2\u7ec6\u80de\u4f53\u79ef\u5206\u5e03\u5bbd\u5ea6",
				"index_unit": "",
				"index_id": 22704
			}, {
				"index_flag": "0",
				"index_value": "5.75",
				"index_normal_value": "4.3-5.8",
				"index_name": "\u7ea2\u7ec6\u80de\u8ba1\u6570",
				"index_unit": "",
				"index_id": 22705
			}, {
				"index_flag": "0",
				"index_value": "85.7",
				"index_normal_value": "80-100",
				"index_name": "\u5e73\u5747\u7ea2\u7ec6\u80de\u4f53\u79ef",
				"index_unit": "fL",
				"index_id": 22706
			}, {
				"index_flag": "0",
				"index_value": "5.4",
				"index_normal_value": "3.5-9.5",
				"index_name": "\u767d\u7ec6\u80de\u8ba1\u6570",
				"index_unit": "",
				"index_id": 22707
			}, {
				"index_flag": "0",
				"index_value": "12.9",
				"index_normal_value": "10-15",
				"index_name": "\u7ea2\u7ec6\u80de\u4f53\u79ef\u5206\u5e03\u5bbd\u5ea6\u53d8\u5f02\u7cfb\u6570",
				"index_unit": "%",
				"index_id": 22708
			}, {
				"index_flag": "0",
				"index_value": "7.9",
				"index_normal_value": "3-12",
				"index_name": "\u4e2d\u6027\u7ec6\u80de\u767e\u5206\u6bd4",
				"index_unit": "%",
				"index_id": 22709
			}, {
				"index_flag": "0",
				"index_value": "0.5",
				"index_normal_value": "0.1-1.1",
				"index_name": "\u6dcb\u5df4\u7ec6\u80de\u6570",
				"index_unit": "",
				"index_id": 22710
			}, {
				"index_flag": "0",
				"index_value": "339.0",
				"index_normal_value": "316-354",
				"index_name": "\u5e73\u5747\u7ea2\u7ec6\u80de\u8840\u7ea2\u86cb\u767d\u6d53\u5ea6",
				"index_unit": "g/L",
				"index_id": 22711
			}, {
				"index_flag": "0",
				"index_value": "29.0",
				"index_normal_value": "27-34",
				"index_name": "\u5e73\u5747\u7ea2\u7ec6\u80de\u8840\u7ea2\u86cb\u767d\u542b\u91cf",
				"index_unit": "Pg",
				"index_id": 22712
			}, {
				"index_flag": "0",
				"index_value": "184.0",
				"index_normal_value": "125-350",
				"index_name": "\u8840\u5c0f\u677f\u8ba1\u6570",
				"index_unit": "",
				"index_id": 22713
			}, {
				"index_flag": "0",
				"index_value": "2.6",
				"index_normal_value": "1.8-6.3",
				"index_name": "\u4e2d\u6027\u7ec6\u80de\u6570",
				"index_unit": "",
				"index_id": 22714
			}, {
				"index_flag": "0",
				"index_value": "48.9",
				"index_normal_value": "40.0-75.0",
				"index_name": "\u4e2d\u6027\u7c92\u7ec6\u80de\u767e\u5206\u6bd4",
				"index_unit": "%",
				"index_id": 22715
			}, {
				"index_flag": "0",
				"index_value": "0.49",
				"index_normal_value": "0.35-0.49",
				"index_name": "\u7ea2\u7ec6\u80de\u6bd4\u5bb9",
				"index_unit": "L/L",
				"index_id": 22716
			}, {
				"index_flag": "0",
				"index_value": "43.2",
				"index_normal_value": "20-50",
				"index_name": "\u6dcb\u5df4\u7ec6\u80de\u767e\u5206\u6bd4",
				"index_unit": "%",
				"index_id": 22717
			}, {
				"index_flag": "0",
				"index_value": "11.6",
				"index_normal_value": "6.8-13.5",
				"index_name": "\u5e73\u5747\u8840\u5c0f\u677f\u4f53\u79ef",
				"index_unit": "fL",
				"index_id": 22718
			}, {
				"index_flag": "0",
				"index_value": "167.0",
				"index_normal_value": "130-175",
				"index_name": "\u8840\u7ea2\u86cb\u767d",
				"index_unit": "g/L",
				"index_id": 22719
			}, {
				"index_flag": "3",
				"index_value": "",
				"index_normal_value": "",
				"index_name": "\u8840\u5c0f\u677f\u4f53\u79ef\u5206\u5e03\u5bbd\u5ea6",
				"index_unit": "",
				"index_id": 22720
			}],
			"\u6027\u522b": "",
			"\u62a5\u544a\u65e5\u671f": "",
			"\u5e74\u9f84": "",
			"\u533b\u9662": "",
			"\u79d1\u5ba4": "",
			"\u68c0\u9a8c\u65e5\u671f": "",
			"\u56fe\u7247\u540d\u79f0": "shumei_1545018954_7751_docx.jpg"
		}],
		"pres": []
	},
	"ret_cd": 200,
	"suc_id": 1
}