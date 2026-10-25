
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="theme-color" content="#101827">
<title>MR Dave Store | Fashion</title>
<style>
*{box-sizing:border-box}
body{margin:0;font-family:Arial,sans-serif;background:#f5f6fa;color:#172033}
header{background:#101827;color:white;padding:22px 16px}
header h1{margin:0;font-size:27px}
header p{color:#d1d5db}
main{max-width:900px;margin:auto;padding:16px}
input,select,button{font:inherit;padding:12px;border-radius:8px}
input,select{width:100%;border:1px solid #ccd2dd;margin:6px 0;background:white}
button{background:#101827;color:white;border:0;cursor:pointer}
.products{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:12px}
.card,.panel{background:white;padding:13px;border-radius:13px;margin-bottom:12px}
.picture{font-size:50px;text-align:center;padding:22px;background:#edf0f7;border-radius:10px}
.price{font-weight:bold;color:#087f5b}
.card button{width:100%}
.panel{margin-top:18px}
.muted{font-size:13px;color:#667085}
.cartrow{display:flex;justify-content:space-between;gap:8px;margin:12px 0}
a{color:inherit}
@media(min-width:650px){.products{grid-template-columns:repeat(3,minmax(0,1fr))}}
</style>
</head>
<body>
<header>
  <h1>MR Dave Store</h1>
  <p>Look good. Feel confident. Shop with us.</p>
  <input id="search" placeholder="Search clothes and shoes..." oninput="render()">
</header>

<main>
  <h2>Shop our collection</h2>
  <p class="muted">Sample products and prices. Update these before selling.</p>
  <div class="products" id="products"></div>

  <section class="panel">
    <h2>Your cart</h2>
    <div id="cart">Your cart is empty.</div>
    <h3 id="total">Total: ₦0</h3>
  </section>

  <section class="panel">
    <h2>Delivery and checkout</h2>
    <form id="checkout">
      <input id="name" placeholder="Full name" required>
      <input id="phone" type="tel" placeholder="Customer phone number" required>
      <input id="address" placeholder="Full delivery address" required>
      <select id="payment" required>
        <option value="Cash on delivery">Cash on delivery</option>
        <option value="Bank transfer">Bank transfer</option>
      </select>
      <button type="submit">Send order to WhatsApp</button>
    </form>
    <p class="muted">
      Your order is sent to the store owner for confirmation.
      Bank details and online payments are not connected yet.
    </p>
    <p id="message" role="status"></p>
  </section>

  <p>Questions? <a href="https://wa.me/2347083347251"
  target="_blank" rel="noopener">Chat with MR Dave Store</a></p>
</main>

<script>
const storePhone = "2347083347251";

const products = [
 {id:1,name:"Classic T-shirt",price:8500,icon:"👕"},
 {id:2,name:"Fashion Sneakers",price:25000,icon:"👟"},
 {id:3,name:"Casual Hoodie",price:18000,icon:"🧥"},
 {id:4,name:"Stylish Handbag",price:14000,icon:"👜"},
 {id:5,name:"Baseball Cap",price:5000,icon:"🧢"},
 {id:6,name:"Classic Wristwatch",price:12000,icon:"⌚"}
];

let cart = [];

function money(n){
 return "₦" + n.toLocaleString("en-NG");
}

function render(){
 const query = document.getElementById("search").value.toLowerCase();

 document.getElementById("products").innerHTML =
 products.filter(p=>p.name.toLowerCase().includes(query))
 .map(p=>`
 <article class="card">
   <div class="picture">${p.icon}</div>
   <h3>${p.name}</h3>
   <p class="price">${money(p.price)}</p>
   <button onclick="add(${p.id})">Add to cart</button>
 </article>`).join("");

 document.getElementById("cart").innerHTML = cart.length
 ? cart.map(i=>{
   const p=products.find(x=>x.id===i.id);
   return `<div class="cartrow">
     <span>${p.name} × ${i.qty}</span>
     <span>${money(p.price*i.qty)}</span>
     <button type="button" onclick="removeItem(${p.id})">−</button>
   </div>`;
 }).join("")
 : "Your cart is empty.";

 const total=cart.reduce((sum,i)=>
 sum+products.find(p=>p.id===i.id).price*i.qty,0);

 document.getElementById("total").textContent="Total: "+money(total);
}

function add(id){
 const item=cart.find(i=>i.id===id);
 if(item)item.qty++;
 else cart.push({id,qty:1});
 render();
}

function removeItem(id){
 const item=cart.find(i=>i.id===id);
 if(item.qty>1)item.qty--;
 else cart=cart.filter(i=>i.id!==id);
 render();
}

document.getElementById("checkout").addEventListener("submit",function(e){
 e.preventDefault();

 if(!cart.length){
   document.getElementById("message").textContent=
   "Add a product before placing your order.";
   return;
 }

 const name=document.getElementById("name").value.trim();
 const phone=document.getElementById("phone").value.trim();
 const address=document.getElementById("address").value.trim();
 const payment=document.getElementById("payment").value;

 const lines=cart.map(i=>{
   const p=products.find(x=>x.id===i.id);
   return `${p.name} x ${i.qty} = ${money(p.price*i.qty)}`;
 });

 const total=cart.reduce((sum,i)=>
 sum+products.find(p=>p.id===i.id).price*i.qty,0);

 const message =
 `NEW ORDER - MR DAVE STORE\n\n`+
 `Customer: ${name}\n`+
 `Phone: ${phone}\n`+
 `Delivery address: ${address}\n\n`+
 `Products:\n${lines.join("\n")}\n\n`+
 `Total: ${money(total)}\n`+
 `Payment: ${payment}\n\n`+
 `Please confirm stock, delivery cost and order details.`;

 const url="https://wa.me/"+storePhone+
 "?text="+encodeURIComponent(message);

 window.open(url,"_blank");
});

render();
</script>
</body>
</html>
