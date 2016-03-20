### 功能
每天定时截图，并把截到的图片自动通过邮件发送。
### 说明
代码注释已经非常详细，就不多做说明，需要的朋友自己查看代码即可，主文件Mail.js，截图文件capturePart1.js，capturePart2.js，capturePart3.js，这里只展示了capturePart1.js其他两个类似。值得注意的是有登录权限的网站一定要设置Cookie，需要截取高质量图片的话截取时间一定设置长一些。
### Mail.js
```
/************************************************************************************************************
    *                                     定时发送邮件功能说明：
    *       node.js必备安装模块：node_modules-->phantomjs，nodemailer，node-schedule，moment
    *       涉及JS文件：route-->mail.js，public-->js-->capturePart1.js，capturePart2.js，capturePart3.js
    *       截图保存地址：public-->images-->mainPage.jpeg(1600*4200)
    *       截图url：http://www.***********.com
    *       程序主要思路：
    *           （1）phantomjs截图-->参照http://phantomjs.org/
    *           （2）nodemailer发送邮件-->参照https://www.npmjs.com/package/nodemailer
    *           （3）node-schedule定时-->参照https://www.npmjs.com/package/node-schedule
    *       注意：
    *           改变发件服务器请修改SMTP
    *           改变收件人请修改变量receiver
    *           改变邮件内容请修改变量html
    *           改变邮件附加图片和附件请修改attachments
    *           改变截图功能请修改public-->js-->server.js
    *           改变定时功能请修改变量rule
    *                                                           ------Sweety
***************************************************************************************************************/

//组件引入开始
var schedule = require("node-schedule");
var path = require('path');
var childProcess = require('child_process');
var phantomjs = require('phantomjs');
var nodemailer = require("nodemailer");
var moment = require("moment");

//组件引入结束
/*--------------------------------------------------------------------------------------------------------------------------------------------*/
//变量定义开始
var today;              //今天开始时间
var binPath = phantomjs.path;      //获取phantomjs.exe路径
var jsPath = process.cwd()+"/public/js/";   //获取server.js所在目录
var childArgs;
//capturePart3();
//capturePart1();
//变量定义结束
/*--------------------------------------------------------------------------------------------------------------------------------------------*/
//主程序开始
var rule = new schedule.RecurrenceRule();         //schedule定时器
rule.hour = 11;
rule.minute = 0;
rule.second = 0;              //定时器规则设定（每天11点触发事件）
var j = schedule.scheduleJob(rule, function(){
    var now = moment();
    today = now.clone().add(-1, 'days').format('YYYY-MM-DD');
    capturePart1();  //触发截图事件(邮件发送功能包含在截图事件里边)
});
//主程序结束
/*---------------------------------------------------------------------------------------------------------------------------------------------*/
//phantomjs截图开始(第一张)
function capturePart1(){
    childArgs = [
        path.join(jsPath, 'serverPart1.js'),          //server.js
        ' https://www.hao123.com '   //要截图的url
    ];
    childProcess.execFile(binPath, childArgs, function(err, stdout, stderr) {
        if(err)
        {
            console.log(err);                //打印错误信息
        }else{
            console.log("Captured Part1 Successful !!"); //打印正确信息
            capturePart2();
        }
    });
}
//phantomjs截图结束(第一张)
//phantomjs截图开始(第二张)
function capturePart2(){
    childArgs = [
        path.join(jsPath, 'serverPart2.js'),        //server.js路径
        'https://www.hao123.com '   //要截图的url
    ];
    childProcess.execFile(binPath, childArgs, function(err, stdout, stderr) {
        if(err)
        {
            console.log(err);                 //打印错误信息
        }else{
            console.log("Captured Part2 Successful !!"); //打印正确信息
            capturePart3();
        }
    });
}
//phantomjs截图结束(第二张)
//phantomjs截图开始(第三张)
function capturePart3(){
    childArgs = [
        path.join(jsPath, 'serverPart3.js'),                     //server.js路径
        ' https://www.hao123.com '   //要截图的url
    ];
    childProcess.execFile(binPath, childArgs, function(err, stdout, stderr) {
        if(err)
        {
            console.log(err);           //打印错误信息
        }else{
            console.log("Captured Part3 Successful !!"); //打印正确信息
            sent();                // 触发发送邮件事件
        }
    });
}
//phantomjs截图结束(第三张)
/*-------------------------------------------------------------------------------------------------------------------------------------------------*/
//nodemailer发送邮件开始
function sent(){
    var imgPart1 = fs.readFileSync(process.cwd()+"/public/images/mainPagePart1.jpeg"); //图片来源
    var imgPart2 = fs.readFileSync(process.cwd()+"/public/images/mainPagePart2.jpeg"); //图片来源
    var imgPart3 = fs.readFileSync(process.cwd()+"/public/images/mainPagePart3.jpeg"); //图片来源
    var smtpTransport = nodemailer.createTransport("SMTP",{                  //邮件SMTP设定（发送邮箱服务器必须开启SMTP）
        host: "smtp.xxxxx.com",                      // 主机
        secureConnection: false,                        // 不使用 SSL
        port: 587,                                      // SMTP 端口
        auth: {
            user: "xxxxx@xxxx.com",            //用户名
            pass: "xxxxxx"                          //密码
        }
    });
    var html =  '<meta http-equiv="Content-Type" content="text/html; charset=utf-8">' +
                'XX好:</br>'+
                '&nbsp;&nbsp;下面为【XXXX】日报汇报（'+today+'）的内容,请参考</br>' +
                '&nbsp;&nbsp;（日报详细信息请点击<a href="#" onclick="changeData();">此处登陆</a>查看）'+
                '</br>' +
                '<img src="cid:img1" id="img1">'+
                '<img src="cid:img2" id="img2">'+
                '<img src="cid:img3" id="img3">';//邮件内容（html代码），img唯一指定地址对应cid（见mailOptions设定）
    var receiver = "xxx@xxx.com";//收件人列表
    var cc = "xxxx@xxxx.com,xxx@xxx.com,xxx@xxxx.com";    //抄送人列表
    var bcc = "xxx@xxxx.com,xxx@xxxx.com"; //密抄送人列表
    var mailOptions = {                   //邮件内容选项设定
        from: "<xxx@xxxx.com>",           //发件地址
        //to: "xxx@xxxx.com",
        to: receiver,                     //收件人
        cc:cc,                           //抄送人
        bcc:bcc,                         //密抄送人
        subject:"【XXXX】日报汇报（"+today+"）", //邮件主题
        text: "【XXXX】日报汇报（"+today+"）",  // plaintext body
        html:html,                         //html内容
        attachments: [
            {
            filename: 'mainPagePart1.jpeg',     //图片名称
            contents: imgPart1,              //图片来源
            cid: 'img1'                      //插入图片标识
            },{
                filename: 'mainPagePart2.jpeg',    //图片名称
                contents: imgPart2,                   //图片来源
                cid: 'img2'                       //插入图片标识
            },{
                filename: 'mainPagePart3.jpeg',      //图片名称
                contents: imgPart3,                  //图片来源
                cid: 'img3'                //插入图片标识
            }
        ]
    };
    smtpTransport.sendMail(mailOptions, function(error, response){//发送邮件
        if(error){
            console.log(error);        //打印错误信息
        }else{
            console.log("Sent Successful !!");   //打印正确信息
        }
    });
}
//nodemailer发送邮件结束

function changeData(){

}
/*---------------------------------------------------------------------------------------------------------------------------------------------*/
```

### capturePart1.js

```
//phantomjs截图
var page = require('webpage').create(),
    system = require('system'),
    address;
page.viewportSize = { width:1920, height: 1080};
page.clipRect = { top: 200, left: 210, width: 1680, height: 1530 };
page.customHeaders={"Cookie":"koa:sess=e*******=;koa:sess.sig=pjadZtLAVtiO6-Haw1vnZZWrRm8"};
if (system.args.length === 1) {
    phantom.exit(1);
} else {
    address = system.args[1];
    page.open(address, function (status) {
    });
}
setTimeout(function() {
    console.log("");
    console.log("### STEP 5: Close page and shutdown (with a delay)");
    page.render('./public/images/mainPagePart1.jpeg', {format: 'jpeg', quality: '100'});
    page.close();
    setTimeout(function(){
        phantom.exit();
    }, 3000);
}, 19000);
```