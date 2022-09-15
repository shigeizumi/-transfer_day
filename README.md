<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <style>
        nl {
            border: 2px black;
        }
        body{
            margin-top:30px;
            margin-left: 30px;
        }
    </style>
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js"
        integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous">
    </script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js"
        integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous">
    </script>

    <script>
    function dayload(){
        for(var k=0;k<30;k++){
            if(document.getElementById("after"+k))document.getElementById("after"+k).remove()
        }
        var month=document.getElementById("aftermonth").value
        const endpoint = "https://script.google.com/macros/s/AKfycbyeZjXo0ZqPrrNEOb_ayGM_CKaVyuQb3_B7oyP1aqzgF8pzFoP05t65L97K_Q8OQ4pH/exec?month="+month;
        fetch(endpoint)
        .then(response => response.json())
        /*成功した処理*/
        .then(data => {
            for(i=1;i<data.length;i++){
                var check=data[i].filter(value=>value=="〇")
                if(check.length==0)continue
                var spreadday=new Date(data[i][0]).toString()
                spreadday=spreadday.split(/\s/)[2]
                var afterdate=document.getElementById("afterday")
                var option = "<option id=\"after"+i+"\" value=\""+spreadday+"\" "
                if(i==1)option+="selected "
                option+=">"+spreadday+"</option>"
                afterdate.insertAdjacentHTML("beforeend",option)
            }
        })
        .then(data=>oktime())
    }

    function beforedayload(){
        for(var k=0;k<30;k++){
            if(document.getElementById("before"+k))document.getElementById("before"+k).remove()
        }

        var month=document.getElementById("beforemonth").value
        const endpoint = "https://script.google.com/macros/s/AKfycbyeZjXo0ZqPrrNEOb_ayGM_CKaVyuQb3_B7oyP1aqzgF8pzFoP05t65L97K_Q8OQ4pH/exec?month="+month;
        fetch(endpoint)
        .then(response => response.json())
        /*成功した処理*/
        .then(data => {
            for(i=1;i<data.length;i++){
                var spreadday=new Date(data[i][0]).toString()
                spreadday=spreadday.split(/\s/)[2]
                var beforedate=document.getElementById("beforeday")
                var option = "<option id=\"before"+i+"\" value=\""+spreadday+"\" "
                if(i==1)option+="selected "
                option+=">"+spreadday+"</option>"
                beforedate.insertAdjacentHTML("beforeend",option)
            }
        })
    }

    function monthwrite(selectyear,writemonth,ba){
        var date=new Date() 
        var year=date.getFullYear()
        var day = date.getDate()
        var month = date.getMonth() + 1
        for(j=1;j<=12;j++){
            var target=document.getElementById(ba+"month"+j)
            if(target)target.remove()
        }

        if(year==selectyear)var startmonth=month
        else startmonth=1
        for (i = startmonth; i <= 12; i++) {
            var option = "<option id=\""+ba+"month"+i+"\" value=\""+i+"\""
            if(i==month) option+=" selected"
            option+=">"+i+"</option>"
            writemonth.insertAdjacentHTML("beforeend",option)
        }
    }
    
        function load(){
            var beforeyear=document.getElementById("beforeyear").value
            var afteryear=document.getElementById("afteryear").value
            var beforemonth=document.getElementById("beforemonth")
            var aftermonth=document.getElementById("aftermonth")

            monthwrite(beforeyear,beforemonth,"before")
            monthwrite(afteryear,aftermonth,"after")

            dayload()
            beforedayload()
        }
        
    function oktime(){
        var month=document.getElementById("aftermonth").value
        var date=Number(document.getElementById("afterday").value)
        var time=document.getElementById("time")
        var afterdate=new RegExp("\\s"+document.getElementById("afterday").value+"\\s","i")

        for(var k=0;k<20;k++){
            if(document.getElementById("option"+k))document.getElementById("option"+k).remove()
        }
        //GASのAPIのURL（各自変更してください。）
        const endpoint = "https://script.google.com/macros/s/AKfycbyeZjXo0ZqPrrNEOb_ayGM_CKaVyuQb3_B7oyP1aqzgF8pzFoP05t65L97K_Q8OQ4pH/exec?month="+month;

        //APIを使って非同期データを取得する
        fetch(endpoint)
        .then(response => response.json())
        /*成功した処理*/
        .then(data => {
            for(i=1;i<data.length;i++){
                var spreadday=new Date(data[i][0]).toString()
                if(!spreadday.match(afterdate))continue
                for(j=1;j<data[i].length;j++){
                    if(document.getElementById("course1").checked){
                    if(data[i][j]=="〇"){
                        var option= "<option value=\""+data[0][j]+"\" id=\"option"+j+"\">"+data[0][j]+"</option>"
                        time.insertAdjacentHTML("beforeend",option)
                    }}
                    else{
                        if(data[i][j]=="〇"&&data[i][j+1]=="〇"){
                            var oktime=data[0][j].match(/\d+:\d+/g)[0]+"~"+data[0][j+1].match(/\d+:\d+/g)[1]
                            var option= "<option value=\""+oktime+"\" id=\"option"+j+"\">"+oktime+"</option>"
                            time.insertAdjacentHTML("beforeend",option)
                        }
                    }
            }
    }})
    }

    function send(){
        var name=document.getElementById("name").value
        var address=document.getElementById("email").value
        var beforeyear=document.getElementById("beforeyear").value
        var beforemonth=document.getElementById("beforemonth").value
        var beforeday=document.getElementById("beforeday").value
        var afteryear=document.getElementById("afteryear").value
        var aftermonth=document.getElementById("aftermonth").value
        var afterday=document.getElementById("afterday").value
        var time=document.getElementById("time").value
        var remarks=document.getElementById("freeform").value

        var before=beforeyear+"年"+beforemonth+"月"+beforeday+"日a"
        var after=afteryear+"年"+aftermonth+"月"+afterday+"日　"+time

        if(!name) return alert("生徒氏名を入力してください")
        if(!address) return alert("メールアドレスを入力してください")
        if(time=="--") return alert("希望時間を選択してください")

        const endpoint="https://script.google.com/macros/s/AKfycbwPUBfOPuwJp989qnFNP7CIgN1QS34m47X9CWYHRRlkirjMHNtRq-GSzPswGNyEmdgpYA/exec?info="+name+"?"+address+"?"+before+"?"+after+"?"+remarks
        fetch(endpoint)
        alert("申請完了しました")
    }
    </script>

    <title>振替申請</title>
</head>

<body onload="load()">
    <div class="container">
    <form name="form1">
        <h3>
            振替申請フォーム
        </h3>
        <nl>
            <li>このフォームで申請後、承認メールをもって振替確定とさせていただきます。</li>
            <li>同日に複数希望があった場合、先に申請いただいた方を優先させていただく旨ご了承ください。</li>
            <li>なるべく最新の空き状況を反映しますが、タイミングにより実際の空き状況と異なる場合がございます。</li>
            <li>欠席するレッスンの前日までに申請してください。レッスン当日のキャンセルは１回分のレッスン消化となり、振替出来ませんのでご了承ください。</li>
        </nl>
        <h>
        <p><br>
         生徒氏名<br>
            <input type="text" id="name" name="name" size="40">
        </p>
        <p>
            メールアドレス<br>
            <input type="text" id="email" name="email" size="40">
        </p>
        <p>
            その他、何かございましたらご記入ください<br>
            <textarea id="freeform" cols="90" rows="3"></textarea>
        </p>
        <p>
            レッスンコース<br>
            <input type="radio" name="course" id="course1" checked onchange="oktime()"><label for="course1">30分</label>
            <input type="radio" name="course" id="course2" onchange="oktime()"><label for="course2">1時間</label>
        </p>

        <p>
            欠席するレッスン日<br>
            <select id="beforeyear" onchange="load()"><option>2022</option></select>年
            <select id="beforemonth" onchange="beforedayload()"></select>月
            <select id="beforeday"></select>日
        </p>
        <p>
            振替希望日<br>
            <select id="afteryear" onchange="load()"><option>2022</option></select>年
            <select id="aftermonth" onchange="dayload()"></select>月
            <select id="afterday" onchange="oktime()"></select>日<br>
            希望時間 ※時間が表示されない場合は満員です。メールでお問い合わせください<br>
            <select id="time"><option>--</option></select>
        </p>

        <div>
            <input type="button" value="送信する" onclick="send()">
        </div>
    </h>
        <p><br>
            <iframe src="https://calendar.google.com/calendar/embed?src=df140ksss1oibc4s6hfd1u4k3k%40group.calendar.google.com&ctz=Asia%2FTokyo" style="border: 0" width="800" height="600" frameborder="0" scrolling="no"></iframe>
        </p>
    </form>
</div>
</body>
</html>
