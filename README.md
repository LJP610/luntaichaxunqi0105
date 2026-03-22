<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>轮胎查询管理</title>
<style>
*{box-sizing:border-box;font-family:system-ui,sans-serif}
body{margin:0;padding:12px;max-width:480px;margin:0 auto;background:#f4f4f4}
.page{display:none}
.page.active{display:block}
.bar{display:flex;gap:8px;margin:10px 0}
.bar button{flex:1;padding:10px;border:none;border-radius:8px;background:#007aff;color:white}
input{width:100%;padding:12px;margin:6px 0;border:1px solid #ddd;border-radius:8px}
button{cursor:pointer}
.btn-primary{background:#007aff;color:white;border:none;padding:12px;border-radius:8px;width:100%;margin:6px 0}
.btn-danger{background:#ff3b30;color:white}
.btn-warning{background:#ff9500;color:white}
.result{padding:12px;background:white;border-radius:8px;margin:10px 0}
.list-item{background:white;padding:12px;border-radius:8px;margin:6px 0;display:flex;align-items:center;gap:8px}
.list-item label{flex:1}
</style>
</head>
<body>

<!-- 首页：只有查询 -->
<div id="page-home" class="page active">
  <h3>轮胎位置查询</h3>
  <input id="search-tno" placeholder="输入轮胎号码">
  <button class="btn-primary" onclick="doSearch()">查询</button>
  <div id="search-result" class="result"></div>

  <div class="bar">
    <button onclick="goPage('add')">录入</button>
    <button onclick="goPage('list')">列表</button>
    <button onclick="goPage('clear')">一键删除</button>
  </div>
</div>

<!-- 录入页：单独 -->
<div id="page-add" class="page">
  <h3>录入轮胎信息</h3>
  <input id="add-tno" placeholder="轮胎号码">
  <input id="add-pos" placeholder="位置（如：左前、仓库A03）">
  <button class="btn-primary" onclick="doAdd()">保存</button>
  <button onclick="goPage('home')">返回首页</button>
</div>

<!-- 列表页：勾选删除、单击修改 -->
<div id="page-list" class="page">
  <h3>轮胎列表</h3>
  <div id="list-box"></div>
  <button class="btn-danger" onclick="doBatchDel()">批量删除勾选</button>
  <button onclick="goPage('home')">返回首页</button>
</div>

<!-- 一键删除页：单独 -->
<div id="page-clear" class="page">
  <h3>一键清空所有数据</h3>
  <p style="color:red">此操作不可恢复，请谨慎！</p>
  <button class="btn-danger" onclick="doClearAll()">确认清空所有</button>
  <button onclick="goPage('home')">返回</button>
</div>

<script>
// 数据读写
function getData(){
  let d=localStorage.getItem("tyreData");return d?JSON.parse(d):{}
}
function setData(obj){
  localStorage.setItem("tyreData",JSON.stringify(obj))
}

// 页面切换
let pages=["home","add","list","clear"];
function goPage(id){
  pages.forEach(p=>{
    document.getElementById("page-"+p).classList.remove("active")
  })
  document.getElementById("page-"+id).classList.add("active")
  if(id==="list")renderList()
}

// 查询
function doSearch(){
  let tno=document.getElementById("search-tno").value.trim()
  let d=getData();
  let el=document.getElementById("search-result")
  if(d[tno]){
    el.textContent="位置："+d[tno]
  }else{
    el.textContent="未找到该轮胎"
  }
}

// 录入
function doAdd(){
  let tno=document.getElementById("add-tno").value.trim()
  let pos=document.getElementById("add-pos").value.trim()
  if(!tno||!pos){alert("请填写完整");return}
  let d=getData();d[tno]=pos;setData(d)
  alert("保存成功")
  document.getElementById("add-tno").value=""
  document.getElementById("add-pos").value=""
}

// 渲染列表
function renderList(){
  let d=getData();
  let box=document.getElementById("list-box");
  box.innerHTML="";
  let keys=Object.keys(d);
  if(keys.length===0){
    box.innerHTML="<div style='padding:12px'>暂无数据</div>";
    return
  }
  keys.forEach(k=>{
    let div=document.createElement("div");
    div.className="list-item";
    div.innerHTML=`
      <input type="checkbox" class="batch-cb" value="${k}">
      <label onclick="doEdit('${k}')">${k} — ${d[k]}</label>
    `;
    box.appendChild(div)
  })
}

// 单击修改
function doEdit(tno){
  let d=getData();
  let newPos=prompt("修改位置：",d[tno]||"");
  if(newPos===null||newPos==="")return;
  d[tno]=newPos;setData(d);renderList()
}

// 批量删除勾选
function doBatchDel(){
  let cbs=document.querySelectorAll(".batch-cb:checked");
  if(cbs.length===0){alert("请先勾选");return}
  if(!confirm("确定删除选中项？"))return;
  let d=getData();
  cbs.forEach(cb=>delete d[cb.value]);
  setData(d);renderList()
}

// 一键清空所有
function doClearAll(){
  if(!confirm("确定清空所有数据？不可恢复！"))return;
  localStorage.removeItem("tyreData");
  alert("已清空")
}
</script>
</body>
</html>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Microsoft YaHei", sans-serif;
}

body {
  background-color: #f5f5f5;
  padding: 20px;
}

.container {
  max-width: 800px;
  margin: 0 auto;
  background: white;
  padding: 30px;
  border-radius: 12px;
  box-shadow: 0 0 15px rgba(0,0,0,0.1);
}

h1 {
  text-align: center;
  color: #2c3e50;
  margin-bottom: 30px;
  font-size: 24px;
}

.search-box {
  display: flex;
  gap: 10px;
  margin-bottom: 30px;
}

.search-box input {
  flex: 1;
  padding: 12px 15px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 16px;
}

.search-box button {
  padding: 12px 25px;
  background: #007bff;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 16px;
}

.search-box button:hover {
  background: #0056b3;
}

.result {
  padding: 20px;
  background: #f8f9fa;
  border-radius: 8px;
  min-height: 100px;
  line-height: 1.6;
}
// 简单轮胎数据示例，你可以自己改
const tireData = [
  { code: "195/65R15", info: "适配紧凑型轿车，舒适性好" },
  { code: "205/55R16", info: "中型车常用，均衡型" },
  { code: "225/45R18", info: "运动型轿车，操控好" }
];

function searchTire() {
  const input = document.getElementById("tireInput").value.trim();
  const resultDiv = document.getElementById("result");

  if (!input) {
    resultDiv.innerHTML = "请输入轮胎规格";
    return;
  }

  const find = tireData.find(item => item.code === input);
  if (find) {
    resultDiv.innerHTML = `<strong>${find.code}</strong><br>${find.info}`;
  } else {
    resultDiv.innerHTML = "未找到该规格，请检查输入或补充数据";
  }
}
