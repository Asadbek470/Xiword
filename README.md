# Xiword
aaa
<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <title>WordXIAI</title>
  <style>
    body { margin:0; font-family:Arial, sans-serif; }
    .menu-bar { display:flex; background:#0078d7; color:white; padding:5px; }
    .menu-item { margin:0 10px; cursor:pointer; position:relative; }
    .dropdown {
      display:none; 
      position:absolute; 
      top:25px; 
      left:0; 
      background:white; 
      color:black;
      border:1px solid #ccc;
      padding:5px;
      min-width:150px;
      z-index:100;
    }
    .dropdown button, .dropdown input[type="color"] { 
      display:block; 
      width:100%; 
      padding:5px; 
      border:none; 
      background:none; 
      cursor:pointer; 
      text-align:left; 
    }
    #editor {
      padding:20px;
      min-height:500px;
      border:1px solid #ccc;
      outline:none;
    }
    .dark { background:#1e1e1e; color:#fff; }
  </style>
</head>
<body>
  <div class="menu-bar">
    <div class="menu-item" onclick="toggleDropdown('fileMenu')">Файл
      <div id="fileMenu" class="dropdown">
        <button onclick="newDoc()">Новый</button>
        <button onclick="openDoc()">Открыть</button>
        <button onclick="saveDoc()">Сохранить</button>
        <button onclick="exportPDF()">Экспорт в PDF</button>
      </div>
    </div>
    <div class="menu-item" onclick="toggleDropdown('homeMenu')">Главная
      <div id="homeMenu" class="dropdown">
        <button onclick="applyCmd('bold')">Жирный</button>
        <button onclick="applyCmd('italic')">Курсив</button>
        <button onclick="applyCmd('underline')">Подчеркнутый</button>
        <button onclick="applyCmd('insertOrderedList')">Нумерованный список</button>
        <button onclick="applyCmd('insertUnorderedList')">Маркированный список</button>
        <button onclick="setColor('red')">Красный текст</button>
        <button onclick="setColor('blue')">Синий текст</button>
        <input type="color" onchange="setColor(this.value)">
      </div>
    </div>
    <div class="menu-item" onclick="toggleDropdown('insertMenu')">Вставка
      <div id="insertMenu" class="dropdown">
        <button onclick="insertImage()">Изображение</button>
        <button onclick="insertTable()">Таблица</button>
        <button onclick="insertLink()">Ссылка</button>
      </div>
    </div>
    <div class="menu-item" onclick="toggleDropdown('layoutMenu')">Разметка страницы
      <div id="layoutMenu" class="dropdown">
        <button onclick="applyCmd('justifyLeft')">Выравнивание слева</button>
        <button onclick="applyCmd('justifyCenter')">По центру</button>
        <button onclick="applyCmd('justifyRight')">Выравнивание справа</button>
      </div>
    </div>
    <div class="menu-item" onclick="toggleDropdown('viewMenu')">Вид
      <div id="viewMenu" class="dropdown">
        <button onclick="toggleTheme()">Тёмная/Светлая тема</button>
        <button onclick="toggleFullscreen()">Полноэкранный режим</button>
      </div>
    </div>
    <div class="menu-item" onclick="toggleDropdown('helpMenu')">Справка
      <div id="helpMenu" class="dropdown">
        <p style="padding:5px;">Добро пожаловать в WordXIAI! <br>
        Используйте меню сверху для редактирования документа.<br>
        - "Файл": управление документами.<br>
        - "Главная": форматирование.<br>
        - "Вставка": добавление элементов.<br>
        - "Разметка": выравнивание текста.<br>
        - "Вид": темы и экран.<br>
        </p>
      </div>
    </div>
  </div>

  <div id="editor" contenteditable="true">
    <h1>WordXIAI</h1>
    <p>Ваш новый текстовый редактор.</p>
  </div>

  <script>
    let savedRange = null;

    // Сохраняем выделение при клике в редакторе
    document.getElementById("editor").addEventListener("mouseup", saveSelection);
    document.getElementById("editor").addEventListener("keyup", saveSelection);

    function saveSelection() {
      const sel = window.getSelection();
      if (sel.rangeCount > 0) {
        savedRange = sel.getRangeAt(0);
      }
    }

    function restoreSelection() {
      if (savedRange) {
        const sel = window.getSelection();
        sel.removeAllRanges();
        sel.addRange(savedRange);
      }
    }

    function applyCmd(cmd, val = null) {
      restoreSelection();
      document.execCommand(cmd, false, val);
      saveSelection(); // сохраняем снова, чтобы не потерялось
    }

    function setColor(color) {
      applyCmd("foreColor", color);
    }

    function insertImage() {
      restoreSelection();
      const url = prompt("Введите URL картинки:");
      if (url) applyCmd("insertImage", url);
    }

    function insertLink() {
      restoreSelection();
      const url = prompt("Введите URL ссылки:");
      if (url) applyCmd("createLink", url);
    }

    function insertTable() {
      restoreSelection();
      let rows = prompt("Количество строк:");
      let cols = prompt("Количество столбцов:");
      if(rows > 0 && cols > 0){
        let table = "<table border='1' style='border-collapse:collapse;width:100%'>";
        for(let r=0;r<rows;r++){
          table+="<tr>";
          for(let c=0;c<cols;c++){ table+="<td>&nbsp;</td>"; }
          table+="</tr>";
        }
        table+="</table>";
        document.execCommand("insertHTML", false, table);
      }
    }

    function toggleTheme() { document.body.classList.toggle("dark"); }

    function toggleFullscreen() {
      if (!document.fullscreenElement) document.documentElement.requestFullscreen();
      else document.exitFullscreen();
    }

    function saveDoc() {
      const text = document.getElementById("editor").innerHTML;
      const blob = new Blob([text], { type: "text/html" });
      const a = document.createElement("a");
      a.href = URL.createObjectURL(blob);
      a.download = "document.html";
      a.click();
    }

    function newDoc() { document.getElementById("editor").innerHTML = ""; }
    function openDoc() { alert("Открытие файлов пока в разработке."); }
    function exportPDF() { alert("Экспорт в PDF пока в разработке."); }

    function toggleDropdown(id) {
      document.querySelectorAll(".dropdown").forEach(d=>d.style.display="none");
      const menu=document.getElementById(id);
      if(menu) menu.style.display="block";
    }
    document.body.addEventListener("click",(e)=>{
      if(!e.target.closest(".menu-item")) {
        document.querySelectorAll(".dropdown").forEach(d=>d.style.display="none");
      }
    });
  </script>
</body>
</html>
