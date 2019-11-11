# Python爬虫: 拉钩网招聘信息爬取

## 项目目标
爬取拉钩网上的指定地区的招聘信息

**例如:**<br> 
>* 抓取拉钩网上所有的地域为 "北京" 的实习机会. 
>* 将结果组织成结构化数据存储在数据库中.

## 网站结构分析
### 入口页面
https://www.lagou.com/jobs/list_?px=new&gx=%E5%AE%9E%E4%B9%A0&gj=&xl=%E6%9C%AC%E7%A7%91&isSchoolJob=1&city=%E5%8C%97%E4%BA%AC#filterBox

通过这个入口页面, 能够看到网页中涵盖以下内容:
>* 一共得到了30页的招聘信息;
>* 这些信息按照 "最新" 规则排序
>* 每一页有15条数据

另外值得注意的是, 切换 "第几页" 的时候, url没有发生变化, 因此切换页使用了异步的方式获取到的页面内容(例如ajax).

### 使用fiddler抓包分析入口页面的http过程
使用fiddler抓包分析入口页面. 可以看到, 在切换页面的时候, 果然有一个ajax请求.

**请求**

    POST https://www.lagou.com/jobs/positionAjax.json?xl=%E6%9C%AC%E7%A7%91&px=new&gx=%E5%AE%9E%E4%B9%A0&city=%E5%8C%97%E4%BA%AC&needAddtionalResult=false&isSchoolJob=1 HTTP/1.1
    Host: www.lagou.com
    Connection: keep-alive
    Content-Length: 20
    Origin: https://www.lagou.com
    X-Anit-Forge-Code: 0
    User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.84 Safari/537.36
    Content-Type: application/x-www-form-urlencoded; charset=UTF-8
    Accept: application/json, text/javascript, */*; q=0.01
    X-Requested-With: XMLHttpRequest
    X-Anit-Forge-Token: None
    Referer: https://www.lagou.com/jobs/list_?xl=%E6%9C%AC%E7%A7%91&px=new&gx=%E5%AE%9E%E4%B9%A0&city=%E5%8C%97%E4%BA%AC&isSchoolJob=1
    Accept-Encoding: gzip, deflate, br
    Accept-Language: zh-CN,zh;q=0.9
    Cookie: JSESSIONID=ABAAABAAADEAAFI1EB5E2E23688242CC5B5751DE62DD0E1; Hm_lvt_4233e74dff0ae5bd0a3d81c6ccf756e6=1514904043; _ga=GA1.2.1201070621.1514904043; _gid=GA1.2.157813745.1514904043; user_trace_token=20180102224041-e8521590-efca-11e7-ba27-525400f775ce; LGSID=20180102224041-e8521746-efca-11e7-ba27-525400f775ce; PRE_UTM=; PRE_HOST=www.baidu.com; PRE_SITE=https%3A%2F%2Fwww.baidu.com%2Flink%3Furl%3DJrK4mviCal3z8EmN--wgV8AHfiVxagzIao99GVcNIYi%26wd%3D%26eqid%3Da9a6ac170000d681000000025a4b98d7; PRE_LAND=https%3A%2F%2Fwww.lagou.com%2F; LGUID=20180102224041-e8521a25-efca-11e7-ba27-525400f775ce; TG-TRACK-CODE=search_code; X_HTTP_TOKEN=0b6b903866b3883a8bc80d19b21ff50a; index_location_city=%E5%8C%97%E4%BA%AC; SEARCH_ID=b40c37bf42d64ddb8609e961def93763; LGRID=20180102225652-2b03ed29-efcd-11e7-9fc4-5254005c3644; Hm_lpvt_4233e74dff0ae5bd0a3d81c6ccf756e6=1514905014

    first=false&pn=2&kd=

**响应**

    HTTP/1.1 200 OK
    Server: nfs/1.0.0.2
    Date: Tue, 02 Jan 2018 15:08:02 GMT
    Content-Type: application/json;charset=UTF-8
    Connection: keep-alive
    REQUEST_ID: 18785081-4d31-472b-a24d-4e717789bce6
    Pragma: no-cache
    Cache-Control: no-cache, no-store, max-age=0
    Expires: Thu, 01 Jan 1970 00:00:00 GMT
    Content-Language: zh-CN
    Cache-Control: no-cache
    Content-Length: 22024

    {
        "success": true,
        "requestId": null,
        "resubmitToken": null,
        "msg": null,
        "content": {
            "pageNo": 2,
            "pageSize": 15,
            "hrInfoMap": {
                "2551975": {
                    "userId": 1554547,
                    "phone": null,
                    "positionName": "HR",
                    "receiveEmail": null,
                    "realName": "HR",
                    "portrait": "i/image/M00/25/71/CgqKkVcez7CABh2CAAELOib4c0M555.png",
                    "userLevel": "G1",
                    "canTalk": true
                },
                "3534405": {
                    "userId": 7547214,
                    "phone": null,
                    "positionName": "",
                    "receiveEmail": null,
                    "realName": "酒店旅游",
                    "portrait": null,
                    "userLevel": "G1",
                    "canTalk": true
                },
                "3535422": {
                    "userId": 41694,
                    "phone": null,
                    "positionName": "hr",
                    "receiveEmail": null,
                    "realName": "创业邦",
                    "portrait": "i/image/M00/56/39/CgpFT1mEQ3OAQ5mOAABOYewW-S0888.jpg",
                    "userLevel": "G1",
                    "canTalk": true
                },
                "3559248": {
                    "userId": 8655051,
                    "phone": null,
                    "positionName": "Recruiter",
                    "receiveEmail": null,
                    "realName": "陈津津",
                    "portrait": null,
                    "userLevel": "G1",
                    "canTalk": true
                },
                "3573370": {
                    "userId": 256247,
                    "phone": null,
                    "positionName": "IOS高级工程师",
                    "receiveEmail": null,
                    "realName": "pyzhaopin",
                    "portrait": "i/image2/M00/1E/43/CgoB5loKogqAOAYFAAR6fe_uwaQ152.png",
                    "userLevel": "G1",
                    "canTalk": true
                },
                "3578938": {
                    "userId": 6916633,
                    "phone": null,
                    "positionName": null,
                    "receiveEmail": null,
                    "realName": "yinhaijun",
                    "portrait": null,
                    "userLevel": "G1",
                    "canTalk": true
                },
                "3655142": {
                    "userId": 9021776,
                    "phone": null,
                    "positionName": "招聘专员",
                    "receiveEmail": null,
                    "realName": "zhaoyan",
                    "portrait": "i/image2/M00/2F/6E/CgotOVo5_UiAAqhvAAAR15khimI846.jpg",
                    "userLevel": "G1",
                    "canTalk": true
                },
                "3773238": {
                    "userId": 459913,
                    "phone": null,
                    "positionName": "人力资源专员",
                    "receiveEmail": null,
                    "realName": "picohr",
                    "portrait": "i/image/M00/15/5D/CgqKkVbrlwGAVw0hAAAPj3vXngg183.png",
                    "userLevel": "G1",
                    "canTalk": true
                },
                "3794032": {
                    "userId": 38272,
                    "phone": null,
                    "positionName": "招聘经理",
                    "receiveEmail": null,
                    "realName": "张新曼",
                    "portrait": "i/image2/M00/1A/AB/CgotOVoBWV6ANWLOAAEDOuet4Ks345.jpg",
                    "userLevel": "G1",
                    "canTalk": true
                },
                "3847545": {
                    "userId": 8749078,
                    "phone": null,
                    "positionName": "",
                    "receiveEmail": null,
                    "realName": "vivian.iwork",
                    "portrait": "i/image/M00/7D/32/CgpEMlpLY3GADFEpAAAUkBqEI_s538.jpg",
                    "userLevel": "G1",
                    "canTalk": false
                },
                "3865545": {
                    "userId": 8751754,
                    "phone": null,
                    "positionName": "",
                    "receiveEmail": null,
                    "realName": "hexiaomiao",
                    "portrait": null,
                    "userLevel": "G1",
                    "canTalk": true
                },
                "3874278": {
                    "userId": 1326426,
                    "phone": null,
                    "positionName": "人力资源经理",
                    "receiveEmail": null,
                    "realName": "Lois",
                    "portrait": "i/image/M00/08/DA/CgpEMljbIAqAdbJJAACJ4wZNxds28.jpeg",
                    "userLevel": "G1",
                    "canTalk": true
                },
                "3929329": {
                    "userId": 9339521,
                    "phone": null,
                    "positionName": "招聘助理",
                    "receiveEmail": null,
                    "realName": "程昭璇",
                    "portrait": "i/image2/M00/23/C0/CgoB5loXyb2AJrTMABeHfhq5Qnk550.JPG",
                    "userLevel": "G1",
                    "canTalk": true
                },
                "3986768": {
                    "userId": 6438740,
                    "phone": null,
                    "positionName": "HRBP",
                    "receiveEmail": null,
                    "realName": "物灵智能科技",
                    "portrait": "i/image2/M00/1E/B7/CgotOVoLuZOAYieYAACNvqsZAj0864.png",
                    "userLevel": "G1",
                    "canTalk": true
                },
                "4000634": {
                    "userId": 9638073,
                    "phone": null,
                    "positionName": "搜狐教育",
                    "receiveEmail": null,
                    "realName": "张先生",
                    "portrait": "i/image/M00/7D/43/CgpEMlpLfwKAZqPXAACT1hgPV80736.png",
                    "userLevel": "G1",
                    "canTalk": true
                }
            },
            "positionResult": {
                "totalCount": 2996,
                "resultSize": 15,
                "hotLabels": null,
                "locationInfo": {
                    "city": "北京",
                    "district": null,
                    "queryByGisCode": false,
                    "businessZone": null,
                    "locationCode": null,
                    "isAllhotBusinessZone": false
                },
                "queryAnalysisInfo": {
                    "positionName": null,
                    "companyName": null,
                    "industryName": null,
                    "usefulCompany": false
                },
                "strategyProperty": {
                    "name": "dm-csearch-useUserAllInterest",
                    "id": 0
                },
                "result": [
                    {
                        "companyId": 50702,
                        "companyShortName": "美团点评",
                        "createTime": "2018-01-02 22:08:36",
                        "positionId": 3534405,
                        "score": 0,
                        "positionAdvantage": "大平台",
                        "salary": "2k-4k",
                        "workYear": "不限",
                        "education": "本科",
                        "city": "北京",
                        "positionName": "HR实习生",
                        "companyLogo": "i/image/M00/6A/05/Cgp3O1gW8zSAUwUsAABMptH-XY087.jpeg",
                        "financeStage": "D轮及以上",
                        "industryField": "移动互联网,O2O",
                        "jobNature": "实习",
                        "companySize": "2000人以上",
                        "approve": 1,
                        "companyLabelList": [
                            "技能培训",
                            "绩效奖金",
                            "岗位晋升",
                            "领导好"
                        ],
                        "publisherId": 7547214,
                        "district": "朝阳区",
                        "positionLables": [
                            "统计"
                        ],
                        "industryLables": [],
                        "businessZones": [
                            "望京",
                            "来广营",
                            "望京",
                            "来广营"
                        ],
                        "adWord": 0,
                        "longitude": "116.48864",
                        "latitude": "40.007096",
                        "imState": "disabled",
                        "lastLogin": 1514901974000,
                        "explain": null,
                        "plus": null,
                        "pcShow": 0,
                        "appShow": 0,
                        "deliver": 0,
                        "gradeDescription": null,
                        "promotionScoreExplain": null,
                        "firstType": "综合职能类",
                        "secondType": "人力资源",
                        "isSchoolJob": 1,
                        "subwayline": "15号线",
                        "stationname": "望京东",
                        "linestaion": "15号线_望京东",
                        "formatCreateTime": "22:08发布",
                        "companyFullName": "北京三快在线科技有限公司"
                    },
                    {
                        "companyId": 3328,
                        "companyShortName": "好未来",
                        "createTime": "2018-01-02 21:45:03",
                        "positionId": 3573370,
                        "score": 0,
                        "positionAdvantage": "免费晚餐，团队氛围好，行业有发展",
                        "salary": "2k-4k",
                        "workYear": "不限",
                        "education": "本科",
                        "city": "北京",
                        "positionName": "人力资源实习生",
                        "companyLogo": "i/image/M00/5B/50/CgpEMlmIKKmAcuxkAAA5bp2u1EY990.png",
                        "financeStage": "上市公司",
                        "industryField": "移动互联网,教育",
                        "jobNature": "实习",
                        "companySize": "2000人以上",
                        "approve": 1,
                        "companyLabelList": [
                            "上市公司",
                            "领军企业",
                            "五险一金",
                            "住福计划"
                        ],
                        "publisherId": 256247,
                        "district": "海淀区",
                        "positionLables": [
                            "HR",
                            "人事",
                            "校园"
                        ],
                        "industryLables": [],
                        "businessZones": [
                            "双榆树",
                            "中关村",
                            "人民大学"
                        ],
                        "adWord": 0,
                        "longitude": "116.323585",
                        "latitude": "39.964647",
                        "imState": "today",
                        "lastLogin": 1514902906000,
                        "explain": null,
                        "plus": null,
                        "pcShow": 0,
                        "appShow": 0,
                        "deliver": 0,
                        "gradeDescription": null,
                        "promotionScoreExplain": null,
                        "firstType": "综合职能类",
                        "secondType": "人力资源",
                        "isSchoolJob": 1,
                        "subwayline": "10号线",
                        "stationname": "海淀黄庄",
                        "linestaion": "4号线大兴线_魏公村;4号线大兴线_人民大学;4号线大兴线_海淀黄庄;10号线_海淀黄庄;10号线_知春里",
                        "formatCreateTime": "21:45发布",
                        "companyFullName": "北京世纪好未来教育科技有限公司"
                    },
                    {
                        "companyId": 212531,
                        "companyShortName": "搜狐视频",
                        "createTime": "2018-01-02 20:44:52",
                        "positionId": 4000634,
                        "score": 0,
                        "positionAdvantage": "优秀转正,锻炼机会多,导师制",
                        "salary": "3k-4k",
                        "workYear": "应届毕业生",
                        "education": "本科",
                        "city": "北京",
                        "positionName": "产品经理实习生",
                        "companyLogo": "i/image/M00/35/E8/CgpEMlk-bVOAEi6qAAAnRY7HNUU312.png",
                        "financeStage": "上市公司",
                        "industryField": "移动互联网,广告营销",
                        "jobNature": "实习",
                        "companySize": "2000人以上",
                        "approve": 0,
                        "companyLabelList": [],
                        "publisherId": 9638073,
                        "district": "海淀区",
                        "positionLables": [
                            "web",
                            "移动",
                            "运营"
                        ],
                        "industryLables": [],
                        "businessZones": [
                            "中关村"
                        ],
                        "adWord": 0,
                        "longitude": "0.0",
                        "latitude": "0.0",
                        "imState": "today",
                        "lastLogin": 1514896843000,
                        "explain": null,
                        "plus": null,
                        "pcShow": 0,
                        "appShow": 0,
                        "deliver": 0,
                        "gradeDescription": null,
                        "promotionScoreExplain": null,
                        "firstType": "产品/需求/项目类",
                        "secondType": "产品设计/需求分析",
                        "isSchoolJob": 1,
                        "subwayline": null,
                        "stationname": null,
                        "linestaion": null,
                        "formatCreateTime": "20:44发布",
                        "companyFullName": "飞狐信息技术（天津）有限公司"
                    },
                    {
                        "companyId": 62,
                        "companyShortName": "今日头条",
                        "createTime": "2018-01-02 20:27:39",
                        "positionId": 3865545,
                        "score": 0,
                        "positionAdvantage": "弹性工作，免费三餐，租房补贴，休闲下午茶，扁平管理，健身瑜伽，过亿用户，职业大牛，晋升空间，团队氛围好",
                        "salary": "4k-5k",
                        "workYear": "不限",
                        "education": "本科",
                        "city": "北京",
                        "positionName": "商业产品实习生-产品设计方向",
                        "companyLogo": "image1/M00/00/01/Cgo8PFTUV_OAH8cPAACZoNxm1EI176.jpg",
                        "financeStage": "C轮",
                        "industryField": "移动互联网,数据服务",
                        "jobNature": "实习",
                        "companySize": "500-2000人",
                        "approve": 1,
                        "companyLabelList": [
                            "扁平管理",
                            "弹性工作",
                            "大厨定制三餐",
                            "就近租房补贴"
                        ],
                        "publisherId": 8751754,
                        "district": "海淀区",
                        "positionLables": [
                            "产品经理"
                        ],
                        "industryLables": [],
                        "businessZones": null,
                        "adWord": 0,
                        "longitude": "39.971268",
                        "latitude": "39.971268",
                        "imState": "today",
                        "lastLogin": 1514894759000,
                        "explain": null,
                        "plus": null,
                        "pcShow": 0,
                        "appShow": 0,
                        "deliver": 0,
                        "gradeDescription": null,
                        "promotionScoreExplain": null,
                        "firstType": "产品/需求/项目类",
                        "secondType": "产品设计/需求分析",
                        "isSchoolJob": 1,
                        "subwayline": null,
                        "stationname": null,
                        "linestaion": null,
                        "formatCreateTime": "20:27发布",
                        "companyFullName": "北京字节跳动科技有限公司"
                    },
                    {
                        "companyId": 1650,
                        "companyShortName": "联想集团",
                        "createTime": "2018-01-02 20:23:52",
                        "positionId": 3559248,
                        "score": 0,
                        "positionAdvantage": "专业团队,学习空间,行业TOP,招聘全流程",
                        "salary": "2k-3k",
                        "workYear": "应届毕业生",
                        "education": "本科",
                        "city": "北京",
                        "positionName": "招聘实习生",
                        "companyLogo": "i/image/M00/01/59/Cgp3O1Zn9kCAAW4jAABX8W4A-fw360.jpg",
                        "financeStage": "上市公司",
                        "industryField": "移动互联网,数据服务",
                        "jobNature": "实习",
                        "companySize": "2000人以上",
                        "approve": 1,
                        "companyLabelList": [
                            "技能培训",
                            "待遇优厚",
                            "晋升空间",
                            "五险一金"
                        ],
                        "publisherId": 8655051,
                        "district": "海淀区",
                        "positionLables": [
                            "招聘",
                            "行政",
                            "HR",
                            "人事"
                        ],
                        "industryLables": [
                            "招聘",
                            "行政",
                            "HR",
                            "人事"
                        ],
                        "businessZones": [
                            "西二旗",
                            "龙泽",
                            "回龙观"
                        ],
                        "adWord": 0,
                        "longitude": "116.29846",
                        "latitude": "40.0534",
                        "imState": "today",
                        "lastLogin": 1514895751000,
                        "explain": null,
                        "plus": null,
                        "pcShow": 0,
                        "appShow": 0,
                        "deliver": 0,
                        "gradeDescription": null,
                        "promotionScoreExplain": null,
                        "firstType": "综合职能类",
                        "secondType": "人力资源",
                        "isSchoolJob": 1,
                        "subwayline": "昌平线",
                        "stationname": "西二旗",
                        "linestaion": "13号线_西二旗;昌平线_西二旗",
                        "formatCreateTime": "20:23发布",
                        "companyFullName": "联想（北京）有限公司"
                    },
                    {
                        "companyId": 1575,
                        "companyShortName": "百度",
                        "createTime": "2018-01-02 19:18:49",
                        "positionId": 3929329,
                        "score": 0,
                        "positionAdvantage": "大百度",
                        "salary": "4k-5k",
                        "workYear": "应届毕业生",
                        "education": "本科",
                        "city": "北京",
                        "positionName": "算法策略实习生",
                        "companyLogo": "i/image/M00/21/3E/CgpFT1kVdzeAJNbUAABJB7x9sm8374.png",
                        "financeStage": "不需要融资",
                        "industryField": "移动互联网,数据服务",
                        "jobNature": "实习",
                        "companySize": "2000人以上",
                        "approve": 1,
                        "companyLabelList": [
                            "股票期权",
                            "弹性工作",
                            "五险一金",
                            "免费班车"
                        ],
                        "publisherId": 9339521,
                        "district": "海淀区",
                        "positionLables": [
                            "机器学习",
                            "推荐",
                            "linux",
                            "C/C++",
                            "python"
                        ],
                        "industryLables": [],
                        "businessZones": [
                            "西北旺",
                            "上地",
                            "马连洼"
                        ],
                        "adWord": 0,
                        "longitude": "116.275105",
                        "latitude": "40.044562",
                        "imState": "today",
                        "lastLogin": 1514891940000,
                        "explain": null,
                        "plus": null,
                        "pcShow": 0,
                        "appShow": 0,
                        "deliver": 0,
                        "gradeDescription": null,
                        "promotionScoreExplain": null,
                        "firstType": "开发/测试/运维类",
                        "secondType": "数据开发",
                        "isSchoolJob": 1,
                        "subwayline": "16号线",
                        "stationname": "马连洼",
                        "linestaion": "16号线_马连洼",
                        "formatCreateTime": "19:18发布",
                        "companyFullName": "百度在线网络技术（北京）有限公司"
                    },
                    {
                        "companyId": 3954,
                        "companyShortName": "杏树林",
                        "createTime": "2018-01-02 19:04:03",
                        "positionId": 3794032,
                        "score": 0,
                        "positionAdvantage": "一日三餐,弹性工时,免费下午茶,成长快",
                        "salary": "2k-4k",
                        "workYear": "不限",
                        "education": "本科",
                        "city": "北京",
                        "positionName": "企业医务室项目实习生",
                        "companyLogo": "i/image/M00/2E/98/CgpFT1k2cu-Ae5eNAAEDTPHkf_A479.jpg",
                        "financeStage": "C轮",
                        "industryField": "移动互联网,医疗健康",
                        "jobNature": "实习",
                        "companySize": "150-500人",
                        "approve": 1,
                        "companyLabelList": [
                            "A类人才",
                            "股票期权",
                            "带薪年假",
                            "年度旅游"
                        ],
                        "publisherId": 38272,
                        "district": "东城区",
                        "positionLables": [
                            "移动互联网",
                            "医疗健康",
                            "AE"
                        ],
                        "industryLables": [
                            "移动互联网",
                            "医疗健康",
                            "AE"
                        ],
                        "businessZones": [
                            "广渠门"
                        ],
                        "adWord": 0,
                        "longitude": "116.447045",
                        "latitude": "39.893135",
                        "imState": "today",
                        "lastLogin": 1514903766000,
                        "explain": null,
                        "plus": null,
                        "pcShow": 0,
                        "appShow": 0,
                        "deliver": 0,
                        "gradeDescription": null,
                        "promotionScoreExplain": null,
                        "firstType": "产品/需求/项目类",
                        "secondType": "项目管理",
                        "isSchoolJob": 1,
                        "subwayline": "10号线",
                        "stationname": "广渠门内",
                        "linestaion": "7号线_广渠门外;7号线_广渠门内;10号线_双井",
                        "formatCreateTime": "19:04发布",
                        "companyFullName": "杏树林信息技术（北京）有限公司"
                    },
                    {
                        "companyId": 189461,
                        "companyShortName": "北京链家",
                        "createTime": "2018-01-02 18:58:42",
                        "positionId": 3655142,
                        "score": 0,
                        "positionAdvantage": "五险一金,节日礼品,晋升空间大,培训完备",
                        "salary": "4k-8k",
                        "workYear": "应届毕业生",
                        "education": "本科",
                        "city": "北京",
                        "positionName": "销售管培生",
                        "companyLogo": "i/image/M00/0C/AF/CgpEMljkoGWAGFI0AAAfGoNNwg8875.jpg",
                        "financeStage": "D轮及以上",
                        "industryField": "生活服务,其他",
                        "jobNature": "实习",
                        "companySize": "2000人以上",
                        "approve": 1,
                        "companyLabelList": [],
                        "publisherId": 9021776,
                        "district": "朝阳区",
                        "positionLables": [
                            "医疗健康",
                            "房产服务",
                            "实习生",
                            "校园"
                        ],
                        "industryLables": [
                            "医疗健康",
                            "房产服务",
                            "实习生",
                            "校园"
                        ],
                        "businessZones": [
                            "酒仙桥"
                        ],
                        "adWord": 0,
                        "longitude": "116.506315",
                        "latitude": "39.971626",
                        "imState": "today",
                        "lastLogin": 1514879253000,
                        "explain": null,
                        "plus": null,
                        "pcShow": 0,
                        "appShow": 0,
                        "deliver": 0,
                        "gradeDescription": null,
                        "promotionScoreExplain": null,
                        "firstType": "市场/商务/销售类",
                        "secondType": "销售",
                        "isSchoolJob": 1,
                        "subwayline": "14号线东段",
                        "stationname": "将台",
                        "linestaion": "14号线东段_将台",
                        "formatCreateTime": "18:58发布",
                        "companyFullName": "北京链家房地产经纪有限公司"
                    },
                    {
                        "companyId": 61921,
                        "companyShortName": "人人行(借贷宝)",
                        "createTime": "2018-01-02 18:56:32",
                        "positionId": 2551975,
                        "score": 0,
                        "positionAdvantage": "九鼎控股；有钱任性；六险一金；福利好",
                        "salary": "4k-5k",
                        "workYear": "不限",
                        "education": "本科",
                        "city": "北京",
                        "positionName": "Java实习",
                        "companyLogo": "i/image/M00/04/01/CgqKkVbFXXqAPo0fAAATqvTo2-I592.png",
                        "financeStage": "C轮",
                        "industryField": "移动互联网,金融",
                        "jobNature": "实习",
                        "companySize": "2000人以上",
                        "approve": 1,
                        "companyLabelList": [
                            "年底双薪",
                            "节日礼物",
                            "绩效奖金",
                            "岗位晋升"
                        ],
                        "publisherId": 1554547,
                        "district": "朝阳区",
                        "positionLables": [],
                        "industryLables": [],
                        "businessZones": null,
                        "adWord": 0,
                        "longitude": null,
                        "latitude": null,
                        "imState": "today",
                        "lastLogin": 1514892996000,
                        "explain": null,
                        "plus": null,
                        "pcShow": 0,
                        "appShow": 0,
                        "deliver": 0,
                        "gradeDescription": null,
                        "promotionScoreExplain": null,
                        "firstType": "技术",
                        "secondType": "后端开发",
                        "isSchoolJob": 1,
                        "subwayline": null,
                        "stationname": null,
                        "linestaion": null,
                        "formatCreateTime": "18:56发布",
                        "companyFullName": "人人行科技有限公司"
                    },
                    {
                        "companyId": 27735,
                        "companyShortName": "亿欧",
                        "createTime": "2018-01-02 18:55:28",
                        "positionId": 3578938,
                        "score": 0,
                        "positionAdvantage": "给予转正机,领导好,地铁附近,环境好",
                        "salary": "4k-6k",
                        "workYear": "不限",
                        "education": "本科",
                        "city": "北京",
                        "positionName": "新媒体运营实习生",
                        "companyLogo": "i/image/M00/65/A6/Cgp3O1gIM8KAOrxjAABJ26tijDY290.jpg",
                        "financeStage": "B轮",
                        "industryField": "移动互联网,O2O",
                        "jobNature": "实习",
                        "companySize": "50-150人",
                        "approve": 1,
                        "companyLabelList": [
                            "专项奖金",
                            "股票期权",
                            "绩效奖金",
                            "五险一金"
                        ],
                        "publisherId": 6916633,
                        "district": "朝阳区",
                        "positionLables": [
                            "自媒体",
                            "微信开发"
                        ],
                        "industryLables": [],
                        "businessZones": null,
                        "adWord": 0,
                        "longitude": "0.0",
                        "latitude": "0.0",
                        "imState": "disabled",
                        "lastLogin": 1514890543000,
                        "explain": null,
                        "plus": null,
                        "pcShow": 0,
                        "appShow": 0,
                        "deliver": 0,
                        "gradeDescription": null,
                        "promotionScoreExplain": null,
                        "firstType": "运营/编辑/客服",
                        "secondType": "运营",
                        "isSchoolJob": 1,
                        "subwayline": null,
                        "stationname": null,
                        "linestaion": null,
                        "formatCreateTime": "18:55发布",
                        "companyFullName": "北京亿欧网盟科技有限公司"
                    },
                    {
                        "companyId": 249709,
                        "companyShortName": "智科特",
                        "createTime": "2018-01-02 18:51:03",
                        "positionId": 3847545,
                        "score": 0,
                        "positionAdvantage": "创意视觉,平面设计,网站设计,弹性工作",
                        "salary": "2k-3k",
                        "workYear": "应届毕业生",
                        "education": "本科",
                        "city": "北京",
                        "positionName": "市场实习生（美工）",
                        "companyLogo": "i/image2/M00/21/71/CgoB5loSsveATvyCAAAUofqEvtY599.jpg",
                        "financeStage": "未融资",
                        "industryField": "数据服务,其他",
                        "jobNature": "实习",
                        "companySize": "少于15人",
                        "approve": 0,
                        "companyLabelList": [],
                        "publisherId": 8749078,
                        "district": "昌平区",
                        "positionLables": [
                            "视觉",
                            "平面",
                            "网页",
                            "品牌"
                        ],
                        "industryLables": [],
                        "businessZones": [
                            "回龙观"
                        ],
                        "adWord": 0,
                        "longitude": "116.34575",
                        "latitude": "40.079499",
                        "imState": "disabled",
                        "lastLogin": 1514890187000,
                        "explain": null,
                        "plus": null,
                        "pcShow": 0,
                        "appShow": 0,
                        "deliver": 0,
                        "gradeDescription": null,
                        "promotionScoreExplain": null,
                        "firstType": "设计类",
                        "secondType": "视觉设计/平面设计",
                        "isSchoolJob": 1,
                        "subwayline": "8号线",
                        "stationname": "平西府",
                        "linestaion": "8号线_回龙观东大街;8号线_平西府;13号线_回龙观",
                        "formatCreateTime": "18:51发布",
                        "companyFullName": "北京智科特机器人科技有限公司"
                    },
                    {
                        "companyId": 151386,
                        "companyShortName": "物灵",
                        "createTime": "2018-01-02 18:50:09",
                        "positionId": 3986768,
                        "score": 0,
                        "positionAdvantage": "AI行业,硅谷范,财务,实习机会",
                        "salary": "3k-5k",
                        "workYear": "应届毕业生",
                        "education": "本科",
                        "city": "北京",
                        "positionName": "财务助理",
                        "companyLogo": "i/image/M00/6E/24/CgqKkVghd2aAIhm3AABeYT_-upQ128.jpg",
                        "financeStage": "不需要融资",
                        "industryField": "硬件,移动互联网",
                        "jobNature": "实习",
                        "companySize": "150-500人",
                        "approve": 1,
                        "companyLabelList": [
                            "带薪年假",
                            "定期体检",
                            "年终分红",
                            "餐补"
                        ],
                        "publisherId": 6438740,
                        "district": "朝阳区",
                        "positionLables": [
                            "金融",
                            "实习",
                            "融资",
                            "税务"
                        ],
                        "industryLables": [
                            "金融",
                            "实习",
                            "融资",
                            "税务"
                        ],
                        "businessZones": [
                            "大山子",
                            "望京",
                            "大山子",
                            "来广营",
                            "望京",
                            "大山子",
                            "望京",
                            "来广营",
                            "来广营"
                        ],
                        "adWord": 0,
                        "longitude": "116.489053",
                        "latitude": "39.99902",
                        "imState": "today",
                        "lastLogin": 1514898985000,
                        "explain": null,
                        "plus": null,
                        "pcShow": 0,
                        "appShow": 0,
                        "deliver": 0,
                        "gradeDescription": null,
                        "promotionScoreExplain": null,
                        "firstType": "金融类",
                        "secondType": "法务财务",
                        "isSchoolJob": 1,
                        "subwayline": "15号线",
                        "stationname": "望京东",
                        "linestaion": "15号线_望京东",
                        "formatCreateTime": "18:50发布",
                        "companyFullName": "北京物灵智能科技有限公司"
                    },
                    {
                        "companyId": 31324,
                        "companyShortName": "标点信息",
                        "createTime": "2018-01-02 18:44:33",
                        "positionId": 3773238,
                        "score": 0,
                        "positionAdvantage": "转正后福利,年终奖金,五险一金,餐补交通补",
                        "salary": "2k-3k",
                        "workYear": "不限",
                        "education": "本科",
                        "city": "北京",
                        "positionName": "记者实习生（北京，自由办公，不需坐班）",
                        "companyLogo": "image1/M00/00/47/CgYXBlTUXOOAfGoJAABFcPT55s0980.jpg",
                        "financeStage": "D轮及以上",
                        "industryField": "企业服务",
                        "jobNature": "实习",
                        "companySize": "150-500人",
                        "approve": 0,
                        "companyLabelList": [
                            "节日礼物",
                            "带薪年假",
                            "绩效奖金",
                            "管理规范"
                        ],
                        "publisherId": 459913,
                        "district": "东城区",
                        "positionLables": [
                            "金融",
                            "编辑"
                        ],
                        "industryLables": [
                            "金融",
                            "编辑"
                        ],
                        "businessZones": [
                            "东四",
                            "北新桥",
                            "景山",
                            "东四",
                            "北新桥",
                            "景山"
                        ],
                        "adWord": 0,
                        "longitude": "116.416357",
                        "latitude": "39.928353",
                        "imState": "today",
                        "lastLogin": 1514889844000,
                        "explain": null,
                        "plus": null,
                        "pcShow": 0,
                        "appShow": 0,
                        "deliver": 0,
                        "gradeDescription": null,
                        "promotionScoreExplain": null,
                        "firstType": "运营/编辑/客服",
                        "secondType": "编辑",
                        "isSchoolJob": 1,
                        "subwayline": "5号线",
                        "stationname": "东四",
                        "linestaion": "2号线_朝阳门;5号线_灯市口;5号线_东四;5号线_张自忠路;5号线_北新桥;6号线_朝阳门;6号线_东四;6号线_南锣鼓巷",
                        "formatCreateTime": "18:44发布",
                        "companyFullName": "CFDA南方医药经济研究所广州标点医药信息有限公司"
                    },
                    {
                        "companyId": 52918,
                        "companyShortName": "泰岳智桥",
                        "createTime": "2018-01-02 18:38:57",
                        "positionId": 3874278,
                        "score": 0,
                        "positionAdvantage": "弹性工作,转正计划,工作居住证,法定节假日",
                        "salary": "2k-3k",
                        "workYear": "不限",
                        "education": "本科",
                        "city": "北京",
                        "positionName": "数据处理专员",
                        "companyLogo": "image1/M00/0A/30/CgYXBlTuhf2ALZUiAABoxjrenr8468.jpg",
                        "financeStage": "不需要融资",
                        "industryField": "移动互联网,社交网络",
                        "jobNature": "实习",
                        "companySize": "150-500人",
                        "approve": 1,
                        "companyLabelList": [
                            "通讯津贴",
                            "五险一金",
                            "弹性工作",
                            "股票期权"
                        ],
                        "publisherId": 1326426,
                        "district": "朝阳区",
                        "positionLables": [
                            "大数据",
                            "数据分析",
                            "数据管理"
                        ],
                        "industryLables": [],
                        "businessZones": [
                            "北苑",
                            "立水桥"
                        ],
                        "adWord": 0,
                        "longitude": "115.99339",
                        "latitude": "39.99426",
                        "imState": "today",
                        "lastLogin": 1514870695000,
                        "explain": null,
                        "plus": null,
                        "pcShow": 0,
                        "appShow": 0,
                        "deliver": 0,
                        "gradeDescription": null,
                        "promotionScoreExplain": null,
                        "firstType": "产品/需求/项目类",
                        "secondType": "数据分析",
                        "isSchoolJob": 1,
                        "subwayline": null,
                        "stationname": null,
                        "linestaion": null,
                        "formatCreateTime": "18:38发布",
                        "companyFullName": "北京泰岳智桥信息技术有限公司"
                    },
                    {
                        "companyId": 4202,
                        "companyShortName": "创业邦",
                        "createTime": "2018-01-02 18:38:41",
                        "positionId": 3535422,
                        "score": 0,
                        "positionAdvantage": "有转正机会,工作氛围好,福利待遇好",
                        "salary": "2k-3k",
                        "workYear": "不限",
                        "education": "本科",
                        "city": "北京",
                        "positionName": "新媒体运营（实习生）",
                        "companyLogo": "i/image/M00/1D/17/CgpEMlkC_MWAI-oGAAAp_EAGEYU746.png",
                        "financeStage": "D轮及以上",
                        "industryField": "移动互联网,企业服务",
                        "jobNature": "实习",
                        "companySize": "150-500人",
                        "approve": 1,
                        "companyLabelList": [
                            "午餐补助",
                            "定期体检",
                            "岗位晋升",
                            "技能培训"
                        ],
                        "publisherId": 41694,
                        "district": "朝阳区",
                        "positionLables": [
                            "编辑"
                        ],
                        "industryLables": [],
                        "businessZones": [
                            "亮马桥",
                            "三元桥",
                            "燕莎"
                        ],
                        "adWord": 0,
                        "longitude": "116.46332",
                        "latitude": "39.956605",
                        "imState": "today",
                        "lastLogin": 1514888332000,
                        "explain": null,
                        "plus": null,
                        "pcShow": 0,
                        "appShow": 0,
                        "deliver": 0,
                        "gradeDescription": null,
                        "promotionScoreExplain": null,
                        "firstType": "运营/编辑/客服",
                        "secondType": "编辑",
                        "isSchoolJob": 1,
                        "subwayline": "10号线",
                        "stationname": "亮马桥",
                        "linestaion": "10号线_三元桥;10号线_亮马桥;机场线_三元桥",
                        "formatCreateTime": "18:38发布",
                        "companyFullName": "爱奇清科（北京）信息科技有限公司"
                    }
                ]
            }
        },
        "code": 0
    }

分析请求报文
>* 这是post请求中的body里的 pn 字段表示了当前是获取第几页的请求.

分析响应报文
>* body部分有一个positionResult字段. 这个字段中有一个result数组. 这个数组里面的内容刚好就是这一页的15条职位信息.
>* 这15条数据的顺序就是页面展示的顺序.
>* 在这15条数据中已经包含了很多有意义的信息(岗位, 待遇, 薪水, 工作地点等)
>* 其中的字段 positionId 就对应了职位详情页的url (形如: https://www.lagou.com/jobs/3535422.html)

## 爬虫概要设计
### 核心逻辑
>* 先遍历 1 - 30 页获取到所有的招聘信息的入口和其他必要信息. 
>* 再通过遍历所有的入口页面, 获取到招聘详细信息.
>* 将获取到的招聘信息写入到数据库中存储.

### 数据库设计

招聘信息表

	position_id
	page
	itemid
	crawler_time
    岗位
    薪水
    经验要求
    发布时间
    公司
    公司类型
    实习/全职
    职位诱惑
    职位描述
    工作地点

id 生成方式: 天级时间戳 * 1000 + 序号(范围 1 - 450)

## 详细设计
### 核心类

	class Job(object):
	    def __init__(self):
	        self.position_id = ''               # 详情页的 url id
			self.page = 0						# 第几页的数据
			self.itemid = 0						# 当前页的第几条数据
	        self.crawler_time = ''              # 抓取页面的当前时间
	        self.position = ''                  # 职位信息
	        self.salary = ''                    # 薪酬待遇
	        self.work_year = ''                 # 工作年限要求
	        self.post_time = ''                 # 岗位发布时间
	        self.company_short_name = ''        # 公司简称
	        self.company_full_name = ''         # 公司全称
	        self.company_type = ''              # 公司类型
	        self.company_size = ''              # 公司规模
	        self.position_type = ''             # 职位类别(详情页)
	        self.position_youhuo = ''           # 职位诱惑(详情页)
	        self.position_desc = ''             # 职位描述(详情页)
	        self.workplace = ''                 # 工作地点(详情页)

### 页面打开模块
**行为**: 基于 liburl2, 模拟请求, 获取页面内容.

**输入**: url

**输出**: 页面html

### 主页解析模块
**行为**: 通过主页链接获取到当前页面的职位列表(职位信息初步)

**输入**: 主页url(带页码)

**输出**: 每个招聘信息的信息和入口页面链接.

### 详细页面解析模块
**行为**: 根据详细页面的入口链接, 抓取详细页面信息.

**输入**: 详细页面的入口链接

**输出**: 详细页面的工作详细要求(结构化数据).

### 主页解析调用模块
**行为**: 创建多个进程的方式, 调用主页解析模块.

**输入**: 主页url(不带页码)

**输出**: 所有页面抓取完毕.

### 数据存储模块
**行为**: 将抓取到的所有结果, 写到数据库中.

**输入**: 保存在内存中的招聘信息(结构化数据).

**输出**: 写到数据库中.

## 模块实现
### 实现打开网页模块
基于 urllib 和 urllib2, 可以获取到对应的拉钩上的页面. 我们入口的url应该是

	https://www.lagou.com/jobs/positionAjax.json?xl=%E6%9C%AC%E7%A7%91&px=new&gx=%E5%AE%9E%E4%B9%A0&city=%E5%8C%97%E4%BA%AC&needAddtionalResult=false&isSchoolJob=1

并且以 post 方式发送请求. 

body 为:

	data = {'first': 'false', 'pn': str(page_num), 'kd': ''}

header 需要参考抓包的结构, 将header中的字段一一构造(注意, 这里可以不构造cookie).

**注意:** 如果缺少 header 字段, 拉钩网会判定该请求为非法请求, 并且给出提示为 "您的操作过于频繁".

### 实现解析主页模块
由于该请求获取到的主页, 实际上是一个 ajax. 返回的结果是一个 json 结构的数据. 因此只需要对响应数据的 body 进行 json 解析操作即可. <br>
json结构参考前面的抓包结果.

	response = json.loads(page)
	# 获得到 result 数组. 数组中的每个元素就是一个招聘信息
    result = response['content']['positionResult']['result']

result中的每一个元素其实是一个字典. 可以取字典中的数据, 拼成一个我们自定义的 Job 对象.

### 实现获取详情页模块
根据主页中得到的 Job 对象, 其中的 positionId 可以用于拼接处详情页的 url. 

详情页 url 形如 

	https://www.lagou.com/jobs/[positionId].html

然后使用一个get请求尝试获取到数据.

	url = 'https://www.lagou.com/jobs/%s.html' % position_id
    response = urllib2.urlopen(url)

发现获取到的结果并不是预期的数据. 而是得到了一个 js, 需要js再次发起请求. 

仍然使用 fiddler 分析页面请求过程, 发现确实是有一个get请求获取到对应的页面. 猜测可能是缺失header信息导致的拿不到预期数据.

**请求:**

	GET https://www.lagou.com/jobs/2513020.html HTTP/1.1
	Host: www.lagou.com
	Connection: keep-alive
	Upgrade-Insecure-Requests: 1
	User-Agent: Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.132 Safari/537.36
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
	Referer: https://www.lagou.com/jobs/list_?px=new&gx=%E5%AE%9E%E4%B9%A0&gj=&xl=%E6%9C%AC%E7%A7%91&isSchoolJob=1&city=%E5%8C%97%E4%BA%AC
	Accept-Encoding: gzip, deflate, br
	Accept-Language: zh-CN,zh;q=0.9
	Cookie: user_trace_token=20180108170937-8220fe0a-1d67-404c-a576-d45d368aa489; _ga=GA1.2.1711485205.1515402581; _gid=GA1.2.1154798746.1515402581; LGUID=20180108170938-a72c0888-f453-11e7-80bc-525400f775ce; JSESSIONID=ABAAABAAAGGABCB8965FD3D2F8CC1CBE3B7C1E1253197C5; SEARCH_ID=c4c8c5f8c6a34b8e858afa76a70732e1; Hm_lvt_4233e74dff0ae5bd0a3d81c6ccf756e6=1515402582,1515474797,1515559471; LGRID=20180110124437-f676fe6a-f5c0-11e7-a026-5254005c3644; Hm_lpvt_4233e74dff0ae5bd0a3d81c6ccf756e6=1515559482
	
**响应:**

	略, 内容即为详情页的html

按照抓包的内容, 补充上 header, 就能抓到正确的数据下来了. 

**注意:** 这里header中最好不要加 `Accept-Encoding: gzip, deflate, br` 字段. 否则抓下来的信息是 gzip 压缩的. 还需要再来一步解压缩. 如果不加上这个字段, 获取到的数据就是原始数据.

**注意:** 这里的cookie字段不能随便造, 否则可能会被对方的反爬虫系统识别. 具体处理方法在后面.

### 实现数据库存储模块
我们使用 SQLite 数据库作为存储媒介

在命令行中, 先创建好数据库和表

    sqlite3 job.db

	create table job(position_id text, 
					crawler_time text, 
					position text, 
					salary text, 
					work_year text, 
					post_time text, 
					company_short_name text, 
					company_full_name text, 
					company_size text, 
					position_type text, 
					position_youhuo text, 
					position_desc text, 
					workplace text);

然后使用代码将数据保存到数据库中即可.

    def save_job(job_results):
        log('start save')
        import sqlite3
        db = sqlite3.connect(config.db_path)
        cursor = db.cursor()
        for i in job_results:
            sql = "insert into job values('%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s')" \
                % (i.position_id, i.crawler_time, i.position, i.salary, i.work_year, i.post_time,
                i.company_short_name, i.company_full_name, i.company_size, i.position_type,
                i.position_youhuo, i.position_desc, i.workplace)
            log(sql)
            cursor.execute(sql)
        db.commit()    # 注意, 必须要 commit 否则数据库的改动不会生效.
        db.close()
        log('finish save')

### 使用定时任务达到每天抓取的效果

    0 10 * * * DATE=`date +\%Y\%m\%d`; cd /home/tangzhong/project/project_code/python_crawler_lagou && python main.py > log/info.${DATE}

## 可能出现的错误
### 出现 HTTP Error 302
错误信息形如

	urllib2.HTTPError: HTTP Error 302: The HTTP server returned a redirect error that would lead to an infinite loop.

对于 urlopen 来说, 如果http请求的状态码不是 200, 就会抛出异常. 因此在这里需要捕捉这个异常.

代码参考

	from urllib2 import Request, urlopen, URLError, HTTPError  
	req = Request(someurl)  
	try:  
	    response = urlopen(req)  
	except HTTPError, e:  
	    print('The server couldn't fulfill the request.')  
	    print('Error code: ', str(e.code))  
	except URLError, e:  
	    print('We failed to reach a server.')  
	    print('Reason: ', e.reason)


### 强制跳转到登陆页面
需要继续抓包来分析, 在观察返回的页面时, 重复请求 N次, 系统就会强制跳转到登陆页面. 

分析页面的返回结果, 发现包含了这样两个字段

	<!-- 动态token，防御伪造请求，重复提交 -->
	window.X_Anti_Forge_Token = '8f5499a3-0670-4e09-839f-3650636df5e8';
    window.X_Anti_Forge_Code = '57113048';

人家的注释写的很清楚, 这就是不让你同一个请求重复提交的. 而且可以观察到, 每次请求之后, 得到的这两个值不相同.

**说明:** 提交请求的时候, 会在cookie中带有一个通过上面两个字段计算出的加密结果, 作为 "暗号"

这就意味着当前我们伪造的请求, 被拉钩的**反爬虫机制**给揪出来了. 那么接下来我们就要想办法绕开反爬虫机制.

当前思路有两个:
>* 伪造暗号
>* 利用 "N次" 的限制.

伪造暗号不太好搞. 因为拉钩的js经过了加密, 以至于不是特别容易分析加密的具体算法. 难度较大. <br>
于是干脆利用 "N次". 既然是同一个请求最多提交N次, 好的, 那就提交N次之后, 换一个请求就可以了.  <br>
问题来了, , , , 服务器是如何识别是 "同一个请求的?" <br>

经过测试, 可以发现, 使用 cookie 中的 `SEARCH_ID`, `user_trace_token`, 和 `JSESSION_ID` 字段共同来标识 "同一个请求"

此时注意, `user_trace_token` 的前半部分标识的是当前的时间戳, 该字段也要好好构造.

构造cookie的代码如下:

	g_offset = 0
	g_count = 0
	
	
	def generate_cookie():
	    # 每隔N次, 重新生成一个search_id等信息
	    global g_offset, g_count
	    if g_count % 2 == 0:
	        g_offset += 1
	    g_count += 1
	    search_id_base = 0x379aed87dc4d4c4ebfb74f94d3ef2206
	    jsessionid_base = 0x66971F2C6528
	    user_trace_base = 0x5ed2dcb50da2;
	    cur_time = datetime.datetime.now().strftime('%Y%m%d%H%M%S')
	    return 'JSESSIONID=ABAAABAACDBABJB6169AE44BB294DAFBBF8%x; ' \
	           'user_trace_token=%s-b32ee3a5-db1e-4328-9a47-%x' \
	           'SEARCH_ID=%x; ' % ((jsessionid_base + g_offset), cur_time, (user_trace_base + g_offset), (search_id_base + g_offset))

其中的 `xxx_base` 是根据实际的真实请求截取的.

### 强制跳转到登录页面(2)
如果抓取速度太快(30秒钟超过50次), 也会被系统强制跳转到登录页面. 这个时候需要让爬虫爬的稍微慢一点. 在合适的位置加上sleep, 放慢抓取速度即可.

## 后序改进方向
>* 多进程爬取
>* 多节点分布式爬虫

## 附录
### fiddler的安装和使用
fiddler是windows下的http抓包工具. 使用便捷, 功能强大. <br>
对我们学习理解http协议帮助非常大.<br>
官网: https://www.telerik.com/fiddler  <br>
下载安装即可. <br>

### beautifulsoup 介绍

使用 pip 安装

    pip install bs4

安装成功之后, 就可以借助 beautifulsoup 帮助完成数据的解析.

如果无法使用pip安装, 也可以自行从 

	https://pypi.python.org/simple/beautifulsoup4/

下载压缩包, 手动解压到

	[Python安装路径]\Lib\site-packages\


 