# LLK1.1
<!DOCTYPE HTML>
<html>
<head>
<meta charset="utf-8">
<title>无聊连连看</title> 
<style type="text/css">
input{
	border-style: solid;
	border-color: #ccc;
	background-color:#ccc;
	height: 30px;
	width: 30px;
	margin:2px;
}
div{

	position: relative;
}
table{
 	position: absolute;
 	border-radius: 10px;
 	left: 450px;
 	top:90px;
 	empty-cells: hide;
 	vertical-align: top;
}
button{
	position: absolute;
	border-style: solid;
	border-radius: 10px;
	left: 572px;
	top: 400px;
}
</style>
<script type="text/javascript">
//按键
var first,second;
//二维数组
var table=new Array();
	for(var i=0;i<10;i++){
		table[i]=new Array();
}
var table_tag=document.getElementsByTagName("input");
//游戏过程中的重新生成
function InitInGame(){
	for (var i = 1;i < 9;i++)
	 {
		for(var j = 1;j < 9;j++){
			var rand=(Math.floor(Math.random() * 7 + 1))+1;
			if(table[i][j]!='')
				table[i][j]=rand;
			else
				table[i][j]='';
		}
	}
	if(check_all(table)==0) InitInGame();
	for (var i = table_tag.length - 1; i >= 0; i--){
		table_tag[i].value=table[Math.floor(parseInt(table_tag[i].name,10)/10)+1][parseInt(table_tag[i].name,10)%10+1];
	}
}
var StartTime;
//初始化
function Init(){
	var Now=new Date();
	StartTime=Date.UTC(Now.getYear(),Now.getMonth(),Now.getDate(),Now.getHours(),Now.getMinutes(),Now.getSeconds(),Now.getMilliseconds());
	first=-1;
	second=-1;
	for(var i=0;i<10;i++){
		table[i][0]=0;
		table[0][i]=0;
		table[i][9]=0;
		table[9][i]=0;
	}
	for (var i = 1;i < 9;i++)
	 {
		for(var j = 1;j < 9;j++){
			var x=(Math.floor(Math.random() * 7 + 1))+1;
				table[i][j]=x;
		}
	}
	if(check_all(table)==0) Init();
	for (var i = table_tag.length - 1; i >= 0; i--){
		table_tag[i].value=table[Math.floor(parseInt(table_tag[i].name,10)/10)+1][parseInt(table_tag[i].name,10)%10+1];
	}
	init_timer();
}
//向左寻找第一个非空格
//op=1->搜索是否有解 op=2->判断两者是否可以消去
function FindLeft(op,NUM,x,y,left,table,x2,y2){
	var re=0;
	var i=x;
	var j=y+1;
	for(j=y+1;j<10;j++){
		if(table[i][j]!=0){
			if(op==1){
				if(table[i][j]==NUM)return -1;
				else return re;
			}
			else{
				if(i==x2&&j==y2)return -1;
				else return re;
			}
		}
		else{
			left[re].x=i;
			left[re].y=j;
			re++;
		}
	}
	return re;
}
//向右
function FindRight(op,NUM,x,y,right,table,x2,y2){
	var re=0;
	var i=x;
	var j=y-1;
	for(;j>=0;j--){
		if(table[i][j]!=0){
			if(op==1){
				if(table[i][j]==NUM)return -1;
				else return re;
			}
			else{
				if(i==x2&&j==y2)return -1;
				else return re;
			}
		}
		else{
			right[re].x=i;
			right[re].y=j;
			re++;
		}
	}
	return re;
}
//向上
function FindUp(op,NUM,x,y,up,table,x2,y2){
	var re=0;
	var i=x-1;
	var j=y;
	for(;i>=0;i--){
		if(table[i][j]!=0){
		   if(op==1){
				if(table[i][j]==NUM)return -1;
				else return re;
			}
			else{
				if(i==x2&&j==y2)return -1;
				else return re;
			}
		}
		else{
			up[re].x=i;
			up[re].y=j;
			re++;
		}
	}
	return re;
}
//向下
function FindDown(op,NUM,x,y,down,table,x2,y2){
	var re=0;
	var i=x+1;
	var j=y;
	for(;i<10;i++){
		if(table[i][j]!=0){
		   if(op==1){
				if(table[i][j]==NUM)return -1;
				else return re;
			}
			else{
				if(i==x2&&j==y2)return -1;
				else return re;
			}
		}
		else{
			down[re].x=i;
			down[re].y=j;
			re++;
		}
	}
	return re;
}
function search(op,NUM,x,y,table,time,dic,x2,y2){
	//将搜寻次数限定在三次
	time--;
	if(time==0) return 0;
	var re;
	//储存空格的对象数组
	var Dirction=new Array();
	for(var i=0;i<10;i++){
		Dirction[i]=new Object();
		Dirction[i].x=0;
		Dirction[i].y=0;
	}
	if(dic!=2){//防止倒退查找
		re=FindLeft(op,NUM,x,y,Dirction,table,x2,y2);
		if(re==-1) { Dirction=null;return 1;}
		else
			 //遍历该方向的空格，以其为搜索中心
			for(var j=0;j<re;j++)
				if(search(op,NUM,Dirction[j].x,Dirction[j].y,table,time,1,x2,y2)==1) return 1;
	}
	if(dic!=1){
		re=FindRight(op,NUM,x,y,Dirction,table,x2,y2);
		if(re==-1) { Dirction=null;return 1;}
		else 
			for(var j=0;j<re;j++)
				if(search(op,NUM,Dirction[j].x,Dirction[j].y,table,time,2,x2,y2)==1) return 1;
	}
	if(dic!=4){
		re=FindUp(op,NUM,x,y,Dirction,table,x2,y2);
		if(re==-1){ Dirction=null;return 1;}
		else
			for(var j=0;j<re;j++)
				if(search(op,NUM,Dirction[j].x,Dirction[j].y,table,time,3,x2,y2)==1) return 1;
	}
	if(dic!=3){
		re=FindDown(op,NUM,x,y,Dirction,table,x2,y2);
		if (re == -1) { Dirction = null; return 1; }
		else
			for (var j = 0; j < re; j++)
				if (search(op,NUM,Dirction[j].x,Dirction[j].y,table,time,4,x2,y2)== 1) return 1;
	}
	return 0;
}
function check_all(table) {
	var time;
	//遍历所有非空格，以其为搜索中心
	for(var i=0;i<10;i++){
		for (var j = 0; j < 10; j++) {
			time=4;
			if(table[i][j]==0) continue;
			else if (search(1,table[i][j], i, j, table, time, 0,0,0) == 1) {return 1;}
		}
	}
	return 0;
}
function myfun(but){
	
	if(first==-1){
		first=but;
	}
	else if(second==-1&&first!=-1){ 
		second=but;
		x1=Math.floor(parseInt(first.name,10)/10)+1;
		y1=parseInt(first.name,10)%10+1;
		x2=Math.floor(parseInt(second.name,10)/10)+1;
		y2=parseInt(second.name,10)%10+1;
		if((first.value==second.value)&&search(2,table[x1][y1],x1,y1,table,4,0,x2,y2)==1&&(x1!=x2||y1!=y2))
			 {
				table[x1][y1]=0;
				table[x2][y2]=0;
				first.value='';
				second.value='';
				first=-1;
				second=-1;
				var flag=1;
				for(var i=0;i<10;i++)
					for(var j=0;j<10;j++){
						if(table[i][j]!=0) flag=0;
					}
				if(flag==1) {
					alert("You Win(such a boring game)!");
					close(this);
				}

				if(check_all(table)!=1) init_1();
				first=-1;
				second=-1;
			}

		else{
			first=second;
			second=-1;
		}
	}
}
function init_timer(){
		window.setInterval('timer()',10);
}
function draw_y(Dtime){
	var cavs=document.getElementById('cavs');
	var ctx=cavs.getContext('2d');
	cavs.width=1000;
	cavs.height=1000;
	draw_bg();
	ctx.lineWidth = 10; 
	ctx.strokeStyle = "#00af0b";
	ctx.lineCap="round";
	ctx.beginPath();
	var percent=Dtime/60000;
	ctx.arc(600,240,230,0, Math.PI*2*percent, false);
	ctx.stroke();
	ctx.closePath();
}
function draw_bg(){
	var cavs=document.getElementById('cavs');
	var ctx=cavs.getContext('2d');
	ctx.lineWidth = 10; 
	ctx.strokeStyle = "#db5c39";
	ctx.lineCap="round";
	ctx.beginPath();
	ctx.arc(600,240,230,0, Math.PI*2, false);
	ctx.stroke();
	ctx.closePath();
}
function timer(){
	var Now=new Date();
	var NowTime=Date.UTC(Now.getYear(),Now.getMonth(),Now.getDate(),Now.getHours(),Now.getMinutes(),Now.getSeconds(),Now.getMilliseconds());
	if(NowTime-StartTime>=60000){
		alert("Time is out!")
		close(this);
	}
	draw_y(NowTime-StartTime);

}
</script>
</head>

<body>
<div>
<canvas id="cavs" width="6000" height="6000"></canvas>
<table id="table" style="background-color: #ccc">
 <tr>
 <td><input type="button" value="1" name='0' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='1' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='2' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='3' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='4' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='5' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='6' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='7' onclick="myfun(this)" /></td>

  </tr>
  <tr>
 <td><input type="button" value="1" name='10' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='11' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='12' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='13' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='14' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='15' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='16' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='17' onclick="myfun(this)" /></td>

  </tr>
 <tr>
 <td><input type="button" value="1" name='20' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='21' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='22' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='23' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='24' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='25' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='26' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='27' onclick="myfun(this)" /></td>
 
  </tr>
   <tr >
 <td><input type="button" value="1" name='30' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='31' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='32' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='33' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='34' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='35' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='36' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='37' onclick="myfun(this)" /></td>
 
  </tr>
	<tr >
 <td><input type="button" value="1" name='40' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='41' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='42' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='43' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='44' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='45' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='46' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='47' onclick="myfun(this)" /></td>
 
  </tr>
	<tr>
 <td><input type="button" value="1" name='50' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='51' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='52' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='53' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='54' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='55' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='56' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='57' onclick="myfun(this)" /></td>
  </tr>
	<tr>
 <td><input type="button" value="1" name='60' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='61' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='62' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='63' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='64' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='65' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='66' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='67' onclick="myfun(this)" /></td>
 
  </tr>
	 <tr>
 <td><input type="button" value="1" name='70' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='71' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='72' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='73' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='74' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='75' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='76' onclick="myfun(this)" /></td>
 <td><input type="button" value="1" name='77' onclick="myfun(this)" /></td>
 
  </tr>
</table>
<button style="color: red" onclick="Init()">START!</button>
</div>
</body>
</html>
