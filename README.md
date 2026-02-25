<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Centiwise Book Manager</title>

<style>
:root{
  --brand:#05AD8F;
  --bg:#f8fafc;
  --card:#ffffff;
  --border:#e5e7eb;
  --text:#0f172a;
  --sub:#475569;
  --ok:#16a34a;
  --err:#dc2626;
}

*{box-sizing:border-box}
body{
  margin:0;
  font-family:system-ui,-apple-system,Segoe UI,Roboto;
  background:var(--bg);
  color:var(--text);
}

/* ===== HEADER ===== */
header{
  background:var(--brand);
  color:white;
  padding:18px 20px;
}
.container{max-width:1200px;margin:auto}

.hdr{
  display:flex;
  justify-content:space-between;
  align-items:center;
}

.brand{
  font-size:20px;
  font-weight:700;
}
.sub{font-size:13px;opacity:.9}

/* ===== SUMMARY ===== */
.summary{
  display:grid;
  grid-template-columns:repeat(auto-fit,minmax(220px,1fr));
  gap:12px;
  margin-top:14px;
}

.tile{
  background:white;
  color:black;
  padding:14px;
  border-radius:12px;
}

/* ===== MAIN ===== */
main{padding:20px}
.grid{
  display:grid;
  grid-template-columns:1fr 1.4fr;
  gap:16px;
}
@media(max-width:900px){.grid{grid-template-columns:1fr}}

.card{
  background:white;
  padding:16px;
  border-radius:14px;
  box-shadow:0 6px 20px rgba(0,0,0,.08);
}

label{
  font-size:12px;
  color:var(--sub);
  display:block;
  margin-top:10px;
}

input,button{
  width:100%;
  padding:10px;
  border-radius:10px;
  border:1px solid var(--border);
  margin-top:6px;
}

button{
  background:var(--brand);
  color:white;
  border:none;
  font-weight:600;
  cursor:pointer;
  margin-top:14px;
}

.list{
  display:flex;
  flex-direction:column;
  gap:10px;
  max-height:480px;
  overflow:auto;
}

.book{
  border:1px solid var(--border);
  border-radius:10px;
  padding:10px;
}

.badge{
  font-size:11px;
  padding:3px 8px;
  border-radius:999px;
}
.available{background:#dcfce7;color:#166534}
.borrowed{background:#fee2e2;color:#991b1b}
</style>
</head>

<body>

<header>
  <div class="container">
    <div class="hdr">
      <div>
        <div class="brand">Centiwise</div>
        <div class="sub">Book Lending Manager</div>
      </div>
      <div class="sub">Nairobi</div>
    </div>

    <div class="summary">
      <div class="tile">Total Books<br><strong id="total">0</strong></div>
      <div class="tile">Available<br><strong id="available">0</strong></div>
      <div class="tile">Borrowed<br><strong id="borrowed">0</strong></div>
    </div>
  </div>
</header>

<main class="container">
  <div class="grid">

    <!-- ADD BOOK -->
    <div class="card">
      <h3>Add Book</h3>
      <label>Title</label>
      <input id="title">
      <label>Author</label>
      <input id="author">
      <label>Category</label>
      <input id="category">
      <button onclick="addBook()">Add Book</button>
    </div>

    <!-- LIST -->
    <div class="card">
      <h3>Library</h3>
      <div class="list" id="list"></div>
    </div>

  </div>
</main>

<script>
let books = JSON.parse(localStorage.getItem("books.v1")||"[]");

const $ = id => document.getElementById(id);

function save(){
  localStorage.setItem("books.v1", JSON.stringify(books));
}

function render(){
  $("total").textContent = books.length;
  $("available").textContent = books.filter(b=>!b.borrower).length;
  $("borrowed").textContent = books.filter(b=>b.borrower).length;

  const list = $("list");
  list.innerHTML = books.length ? "" : "<i>No books added</i>";

  books.forEach((b,i)=>{
    list.innerHTML += `
      <div class="book">
        <strong>${b.title}</strong><br>
        ${b.author} • ${b.category}<br>

        <span class="badge ${b.borrower?'borrowed':'available'}">
          ${b.borrower ? "Borrowed by "+b.borrower : "Available"}
        </span>

        ${!b.borrower ? `
          <input placeholder="Borrower name" id="br${i}">
          <button onclick="borrow(${i})">Borrow</button>
        ` : `
          <button onclick="returnBook(${i})">Return</button>
        `}
      </div>
    `;
  });

  save();
}

function addBook(){
  const t=$("title").value;
  const a=$("author").value;
  const c=$("category").value;
  if(!t||!a) return alert("Title and Author required");

  books.push({title:t,author:a,category:c});
  $("title").value=$("author").value=$("category").value="";
  render();
}

function borrow(i){
  const name = document.getElementById("br"+i).value;
  if(!name) return alert("Enter borrower name");
  books[i].borrower = name;
  render();
}

function returnBook(i){
  delete books[i].borrower;
  render();
}

render();
</script>

</body>
</html>

