# FIND-NEARBY-RESTAURANTS-AND-BOOKING-TABLE-
A full-stack restaurant listing and table booking website for Vadodara built using HTML, CSS, JavaScript, Node.js.
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Find Nearby Restaurants — Full</title>

<!-- Fonts & libraries -->
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>

<style>
  :root{--accent:#ffbb00;--danger:#ff4b2b;--success:#00c853;--muted:#1f1f1f}
  *{box-sizing:border-box}
  html,body{height:100%;margin:0;font-family:'Poppins',sans-serif;background:#121212;color:#fff}
  section{min-height:100vh;display:flex;flex-direction:column;align-items:center;justify-content:center;padding:2rem;background-size:cover;background-position:center;transition:all .6s}
  #login{background-image:url('https://images.unsplash.com/photo-1528605248644-14dd04022da1?auto=format&fit=crop&w=1400&q=80')}
  #home{background-image:url('https://images.unsplash.com/photo-1600891964599-f61ba0e24092?auto=format&fit=crop&w=1400&q=80')}
  #explore{background-image:url('https://images.unsplash.com/photo-1528133111763-6b2c73a20c65?auto=format&fit=crop&w=1400&q=80')}
  #order-summary,#admin-dashboard{background:var(--muted)}
  .login-box{background:rgba(0,0,0,.65);padding:2rem;border-radius:14px;max-width:420px;width:94%;text-align:center;box-shadow:0 10px 40px rgba(0,0,0,.6)}
  .login-box h2{color:var(--accent);margin:0 0 1rem}
  .login-box input,.login-box button{width:100%;padding:.75rem;border-radius:10px;border:none;margin:.45rem 0}
  .login-box input{background:#fff;padding:.6rem}
  .login-box button{background:var(--accent);font-weight:700;cursor:pointer}
  .error-msg{color:var(--danger);font-size:.9rem;margin-top:.35rem}
  .top-actions{display:flex;gap:1rem;align-items:center;justify-content:center;margin:1rem 0;flex-wrap:wrap}
  .btn{padding:.9rem 1.4rem;border-radius:12px;border:none;cursor:pointer;font-weight:700}
  .btn-explore{background:var(--danger);color:#fff}
  .btn-logout{background:#222;color:var(--accent);border:1px solid rgba(255,187,0,.15)}
  .btn-order{background:var(--success);color:#fff;display:none}
  .container{width:95%;max-width:1200px;margin:1rem auto;background:rgba(0,0,0,.6);padding:1rem;border-radius:14px}
  .grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(260px,1fr));gap:1rem}
  .card{background:#fff;color:#000;border-radius:12px;padding:1rem;box-shadow:0 8px 30px rgba(0,0,0,.5);display:flex;flex-direction:column;gap:.5rem}
  .card img{width:100%;border-radius:8px;object-fit:cover;height:160px}
  .card h3{margin:.2rem 0}
  .details{display:none;margin-top:.6rem}
  .booking-form input,.booking-form select{width:100%;padding:.6rem;border-radius:8px;border:1px solid #ccc;margin:.45rem 0}
  .booking-form button{background:var(--danger);color:#fff;border:none;padding:.7rem;border-radius:8px;cursor:pointer}
  .menu-item{margin-top:.6rem}
  .menu-item img{width:100%;border-radius:8px;height:120px;object-fit:cover}
  .menu-row{display:grid;grid-template-columns:1fr 1fr;gap:0.6rem}
  .menu-item button{background:var(--accent);color:#000;border:none;border-radius:8px;padding:.5rem 0.6rem;cursor:pointer;font-weight:700}
  footer{padding:1rem;text-align:center;background:#000;color:var(--accent)}
  table{width:100%;border-collapse:collapse;color:#fff}
  th,td{border:1px solid #444;padding:.6rem;text-align:center}
  .charts-row{display:grid;grid-template-columns:1fr 1fr;gap:1rem;margin-top:1rem}
  @media(max-width:900px){.charts-row{grid-template-columns:1fr} .card img{height:140px}}
</style>
</head>
<body>

<!-- LOGIN -->
<section id="login">
  <div class="login-box">
    <h2>🍽️ FindNearby</h2>
    <input id="username" placeholder="Username (type 'admin' for admin)" />
    <input id="password" type="password" placeholder="Password (admin: 1234)" />
    <button class="btn" onclick="login()">Login</button>
    <div class="error-msg" id="login-error"></div>
    <p style="font-size:.9rem;margin-top:.6rem;color:#ddd">Admin credentials: <strong>admin / 1234</strong></p>
  </div>
</section>

<!-- HOME -->
<section id="home" style="display:none">
  <h1 style="font-size:2.4rem;color:var(--accent)">🍴 Find Nearby Restaurants</h1>
  <div class="top-actions">
    <button class="btn btn-explore" onclick="showExplore()">Explore Restaurants 🍔</button>
    <button class="btn btn-logout" onclick="logout()">Logout</button>
  </div>
</section>

<!-- EXPLORE -->
<section id="explore" style="display:none;min-height:80vh;align-items:flex-start;padding-top:2.5rem">
  <div style="width:95%;max-width:1200px;margin:0.6rem auto;display:flex;justify-content:space-between;align-items:center">
    <h2 style="color:var(--accent)">All Restaurants</h2>
    <div>
      <button class="btn btn-logout" onclick="logout()">Logout</button>
      <button class="btn btn-order" id="view-order-btn" onclick="showOrderSummary()">View My Order 🧾</button>
    </div>
  </div>

  <div class="container">
    <div class="grid" id="restaurant-grid"></div>
  </div>
</section>

<!-- ORDER SUMMARY -->
<section id="order-summary" style="display:none;flex-direction:column;align-items:center">
  <h2 style="color:var(--accent)">🧾 Your Order Summary</h2>
  <div class="container" id="order-list"></div>
  <div style="margin-top:.6rem">
    <button class="btn btn-explore" onclick="returnToExplore()">Back to Explore</button>
  </div>
</section>

<!-- ADMIN DASHBOARD -->
<section id="admin-dashboard" style="display:none;align-items:flex-start;padding-top:2rem">
  <div style="width:95%;max-width:1200px;margin:0 auto;display:flex;justify-content:space-between;align-items:center">
    <h2 style="color:var(--accent)">📊 Admin Dashboard</h2>
    <div>
      <button class="btn btn-logout" onclick="logout()">Logout</button>
      <button class="btn" style="background:#333;color:#fff;margin-left:.5rem" onclick="exportDashboardPDF()">Export Dashboard PDF</button>
    </div>
  </div>

  <div class="container" id="admin-capture-area">
    <h3 style="margin-top:0;color:var(--accent)">Logged-in Users</h3>
    <table><thead><tr><th>#</th><th>Username</th><th>Login Time</th></tr></thead>
      <tbody id="users-table"></tbody></table>

    <h3 style="margin-top:1rem;color:var(--accent)">Bookings</h3>
    <table><thead><tr><th>#</th><th>Name</th><th>Restaurant</th><th>Guests</th><th>Time</th></tr></thead>
      <tbody id="booking-table"></tbody></table>

    <h3 style="margin-top:1rem;color:var(--accent)">Orders</h3>
    <table><thead><tr><th>#</th><th>User</th><th>Restaurant</th><th>Dish</th><th>Price</th></tr></thead>
      <tbody id="orders-table"></tbody></table>

    <div style="margin-top:1rem" class="charts-row">
      <div style="background:#fff;padding:10px;border-radius:8px">
        <canvas id="pieChart"></canvas>
      </div>
      <div style="background:#fff;padding:10px;border-radius:8px">
        <canvas id="barChart"></canvas>
      </div>
    </div>
  </div>
</section>

<footer>© 2025 FindNearby | Built by Himanshi & Aman</footer>

<!-- Modal -->
<div id="modal" style="position:fixed;inset:0;background:rgba(0,0,0,.65);display:none;align-items:center;justify-content:center;z-index:1200">
  <div style="background:#fff;color:#000;padding:1.2rem;border-radius:12px;max-width:420px;width:92%;text-align:center">
    <div id="modal-content"></div>
    <div style="margin-top:.8rem"><button class="btn" onclick="closeModal()" style="background:var(--danger);color:#fff">Close</button></div>
  </div>
</div>

<script>
/* ---------------------------
   App state (in-memory + localStorage)
   --------------------------- */
let currentUser = null;            // string username (non-admin)
let users = JSON.parse(localStorage.getItem('fn_users')||'[]'); // {username, time}
let bookings = JSON.parse(localStorage.getItem('fn_bookings')||'[]'); // {name, restaurant, guests, time}
let orders = JSON.parse(localStorage.getItem('fn_orders')||'[]'); // {user, restaurant, dish, price}
let cart = [];                     // current user's orders before viewing order summary
let pieChartObj = null, barChartObj = null;

/* ---------------------------
   Restaurants + menus (6 restaurants)
   Each restaurant has menu: {name,price,img}
   --------------------------- */
const restaurants = [
  {
    name:"Bazylia",
    cuisine:"Global Cuisine",
    area:"Alkapuri",
    rating:4.5,
    img:"https://d4t7t8y8xqo0t.cloudfront.net/app//resized/818X450/restaurant/704753/restaurant320250526055131.jpg",
    menu:[
      {name:"Peri-Peri Paneer", price:199, img:"https://tse3.mm.bing.net/th/id/OIP.-2CrRSjbv_7RRi87G3OcHQHaJm"},
      {name:"Veg Quesadilla", price:249, img:"https://www.goodlifeeats.com/wp-content/uploads/2022/06/Recipe-for-Veggie-Quesadillas-720x931.jpg"},
      {name:"Sizzler", price:399, img:"https://i.ytimg.com/vi/yl3L9-qbTrY/maxresdefault.jpg"}
    ]
  },
  {
    name:"Feugo - Woodfire Pizza",
    cuisine:"Italian",
    area:"Gotri",
    rating:4.8,
    img:"https://d4t7t8y8xqo0t.cloudfront.net/app//resized/818X450/restaurant/704424/restaurant020250512072345.jpg",
    menu:[
      {name:"Margherita Pizza", price:349, img:"https://laurenslatest.com/wp-content/uploads/2021/01/margherita-pizza-recipe-04.jpg"},
      {name:"Garlic Bread", price:129, img:"https://www.ambitiouskitchen.com/wp-content/uploads/2023/02/Garlic-Bread-4.jpg"},
      {name:"Pasta Alfredo", price:299, img:"https://tse4.mm.bing.net/th/id/OIP.40MxDO_w69OYgZXIAGhE2wAAAA"}
    ]
  },
  {
    name:"Mirch Masala",
    cuisine:"Mughlai & North Indian",
    area:"Race Course",
    rating:4.2,
    img:"https://d4t7t8y8xqo0t.cloudfront.net/app//resized/818X450/restaurant/703584/restaurant120250417053406.png",
    menu:[
      {name:"Butter Chicken", price:329, img:"https://th.bing.com/th/id/R.28acab99b35ca1073cefd5b9497ffe49"},
      {name:"Paneer Tikka", price:259, img:"https://staticcookist.akamaized.net/wp-content/uploads/sites/22/2021/08/paneer-tikka.jpg"},
      {name:"Naan Plate", price:79, img:"https://www.vegrecipesofindia.com/wp-content/uploads/2022/12/garlic-naan-1.jpg"}
    ]
  },
  {
    name:"Milton's South Corner",
    cuisine:"South Indian",
    area:"Akota",
    rating:4.0,
    img:"https://dynamic-media-cdn.tripadvisor.com/media/photo-o/26/de/53/8c/view-from-outside-footpath.jpg",
    menu:[
      {name:"Masala Dosa", price:149, img:"https://apollosugar.com/wp-content/uploads/2018/12/Masala-Dosa-1024x683.jpg"},
      {name:"Idli Sambhar", price:99, img:"https://img.freepik.com/premium-photo/idly-sambar-idli-with-sambhar-green-red-chutney"},
      {name:"Medu Vada", price:129, img:"https://www.vegrecipesofindia.com/wp-content/uploads/2021/07/medu-vada-1.jpg"}
    ]
  },
  {
    name:"Millennium Restaurant",
    cuisine:"Mughlai & Indian Food",
    area:"Fatehgunj",
    rating:4.6,
    img:"https://www.crazymasalafood.com/wp-content/images/1-126.jpg.webp",
    menu:[
      {name:"Dal Makhani", price:189, img:"https://www.cookwithmanali.com/wp-content/uploads/2019/04/Restaurant-Style-Dal-Makhani.jpg"},
      {name:"Chicken Biryani", price:299, img:"https://www.whiskaffair.com/wp-content/uploads/2020/07/Chicken-Biryani-2-3.jpg"},
      {name:"Malai Kofta", price:219, img:"https://tse2.mm.bing.net/th/id/OIP.Q9Au0pSwH8dBYHEpXmfO6gHaHa"}
    ]
  },
  {
    name:"Dawat Restaurant",
    cuisine:"Chinese & Indian",
    area:"Manjalpur",
    rating:4.1,
    img:"https://d4t7t8y8xqo0t.cloudfront.net/app//resized/818X450/restaurant/702677/restaurant020250626090220.png",
    menu:[
      {name:"Chilli Paneer", price:229, img:"https://tse2.mm.bing.net/th/id/OIP.ap9yax7MjhrVb99Ch4E5mwHaE8"},
      {name:"Hakka Noodles", price:169, img:"https://www.indianhealthyrecipes.com/wp-content/uploads/2021/07/hakka-noodles-recipe.jpg"},
      {name:"Veg Manchurian", price:189, img:"https://vegecravings.com/wp-content/uploads/2017/03/veg-manchurian-dry-recipe-step-by-step-instructions-10.jpg"}
    ]
  }
];

/* ---------------------------
   Utilities: persist state
   --------------------------- */
function persist() {
  localStorage.setItem('fn_users', JSON.stringify(users));
  localStorage.setItem('fn_bookings', JSON.stringify(bookings));
  localStorage.setItem('fn_orders', JSON.stringify(orders));
}

/* ---------------------------
   Login / Logout / Navigation
   --------------------------- */
function login() {
  const u = document.getElementById('username').value.trim();
  const p = document.getElementById('password').value.trim();
  document.getElementById('login-error').textContent = '';
  if (!u || !p) {
    document.getElementById('login-error').textContent = 'Please enter username and password.';
    return;
  }

  if (u.toLowerCase() === 'admin' && p === '1234') {
    currentUser = 'admin';
    showAdmin();
    recordLogin(u);
    return;
  }

  // normal user login
  currentUser = u;
  // record login (show to admin)
  recordLogin(u);
  // show home/explore
  document.getElementById('login').style.display = 'none';
  document.getElementById('home').style.display = 'flex';
}

function logout() {
  currentUser = null;
  cart = [];
  document.querySelectorAll('section').forEach(s => s.style.display = 'none');
  document.getElementById('login').style.display = 'flex';
  document.getElementById('username').value = '';
  document.getElementById('password').value = '';
  document.getElementById('view-order-btn').style.display = 'none';
}

/* ---------------------------
   Record user logins (for admin)
   --------------------------- */
function recordLogin(username) {
  const t = new Date().toLocaleString();
  users.push({ username, time: t });
  persist();
}

/* ---------------------------
   Explore / Display restaurants
   --------------------------- */
function showExplore() {
  document.getElementById('home').style.display = 'none';
  document.getElementById('explore').style.display = 'flex';
  document.getElementById('view-order-btn').style.display = cart.length ? 'inline-block' : 'none';
  displayRestaurants();
}

function displayRestaurants() {
  const grid = document.getElementById('restaurant-grid');
  grid.innerHTML = '';
  restaurants.forEach((r, i) => {
    const card = document.createElement('div');
    card.className = 'card';
    card.innerHTML = `
      <h3>${r.name}</h3>
      <p><strong>Cuisine:</strong> ${r.cuisine}</p>
      <p><strong>Area:</strong> ${r.area} | ⭐${r.rating}</p>
      <img src="${r.img}" alt="${r.name}">
      <button class="btn btn-explore" onclick="toggleDetails(${i})">Book / Menu</button>
      <div class="details" id="details-${i}"></div>
    `;
    grid.appendChild(card);

    // build details content separately so we can access elements
    const detailsHTML = `
      <div style="margin-top:.6rem">
        <h4>Book a Table 🍽️</h4>
        <div class="booking-form">
          <input type="text" id="name-${i}" placeholder="Your Name" value="${currentUser && currentUser!=='admin' ? currentUser : ''}">
          <input type="number" id="guests-${i}" min="2" placeholder="No. of Guests (min 2)">
          <select id="time-${i}">
            <option value="">Select Time</option>
            <option>11 AM</option><option>12 PM</option><option>1 PM</option>
            <option>7 PM</option><option>8 PM</option><option>9 PM</option>
          </select>
          <button onclick="confirmBooking(${i})">Book Table</button>
        </div>

        <h4 style="margin-top:.8rem">Menu 🍽️</h4>
        <div class="menu-row">
          ${r.menu.map((m,mi) => `
            <div class="menu-item">
              <h4>${m.name} — ₹${m.price}</h4>
              <img src="${m.img}" alt="${m.name}">
              <div style="margin-top:.45rem">
                <button onclick="addToCart(${i}, ${mi})">Add to Order</button>
              </div>
            </div>`).join('')}
        </div>
      </div>
    `;
    document.getElementById(`details-${i}`).innerHTML = detailsHTML;
  });
}

function toggleDetails(i) {
  const el = document.getElementById(`details-${i}`);
  if (!el) return;
  el.style.display = el.style.display === 'block' ? 'none' : 'block';
}

/* ---------------------------
   Booking
   --------------------------- */
function confirmBooking(i) {
  const name = document.getElementById(`name-${i}`).value.trim();
  const guests = parseInt(document.getElementById(`guests-${i}`).value, 10);
  const time = document.getElementById(`time-${i}`).value;
  if (!name || !guests || guests < 2 || !time) {
    alert('Please fill all booking details correctly (minimum 2 guests).');
    return;
  }
  const booking = { name, restaurant: restaurants[i].name, guests, time };
  bookings.push(booking);
  persist();
  showModal(`✅ Table booked successfully at <strong>${restaurants[i].name}</strong> for <strong>${time}</strong>!`);
  updateAdminDashboard(); // reflect immediately in admin
}

/* ---------------------------
   Orders / Cart
   --------------------------- */
function addToCart(rIdx, mIdx) {
  if (!currentUser || currentUser === 'admin') {
    alert('Please login as a user to order food.');
    return;
  }
  const r = restaurants[rIdx];
  const m = r.menu[mIdx];
  cart.push({ user: currentUser, restaurant: r.name, dish: m.name, price: m.price });
  document.getElementById('view-order-btn').style.display = 'inline-block';
  showModal(`Added <strong>${m.name}</strong> from <strong>${r.name}</strong> to your order.`);
}

function showOrderSummary() {
  document.getElementById('explore').style.display = 'none';
  document.getElementById('order-summary').style.display = 'flex';
  const list = document.getElementById('order-list');
  if (!cart.length) {
    list.innerHTML = '<p>No items in your order yet.</p>';
    return;
  }
  let html = `<div style="text-align:left"><strong>User:</strong> ${currentUser}<hr>`;
  let total = 0;
  cart.forEach((it, idx) => {
    total += it.price;
    html += `<p>(${idx+1}) <strong>${it.dish}</strong> — ${it.restaurant} — ₹${it.price} <button onclick="removeFromCart(${idx})" style="margin-left:.6rem">Remove</button></p>`;
  });
  html += `<hr><p><strong>Total: ₹${total}</strong></p>`;
  html += `<div style="display:flex;gap:.6rem;margin-top:.6rem"><button class="btn btn-explore" onclick="placeOrder()">Place Order</button><button class="btn btn-logout" onclick="clearCart()">Clear</button></div></div>`;
  list.innerHTML = html;
}

function removeFromCart(idx) {
  cart.splice(idx,1);
  showOrderSummary();
  document.getElementById('view-order-btn').style.display = cart.length ? 'inline-block' : 'none';
}

function clearCart() {
  if (!confirm('Clear all items from your cart?')) return;
  cart = [];
  document.getElementById('view-order-btn').style.display = 'none';
  showOrderSummary();
}

function placeOrder() {
  if (!cart.length) { alert('Your cart is empty.'); return; }
  // move cart items to orders (persist)
  cart.forEach(it => orders.push(it));
  persist();
  showModal('✅ Your order has been placed! Admin can view it in Orders.');
  cart = [];
  document.getElementById('view-order-btn').style.display = 'none';
  showOrderSummary();
  updateAdminDashboard();
}

/* ---------------------------
   Admin Dashboard: render users/bookings/orders + charts
   --------------------------- */
function showAdmin() {
  document.getElementById('login').style.display = 'none';
  document.getElementById('admin-dashboard').style.display = 'flex';
  updateAdminDashboard();
}

function updateAdminDashboard() {
  // users table
  const usersTable = document.getElementById('users-table');
  usersTable.innerHTML = '';
  users.forEach((u, i) => {
    usersTable.innerHTML += `<tr><td>${i+1}</td><td>${escapeHtml(u.username)}</td><td>${escapeHtml(u.time)}</td></tr>`;
  });

  // bookings table
  const bookingTable = document.getElementById('booking-table');
  bookingTable.innerHTML = '';
  bookings.forEach((b, i) => {
    bookingTable.innerHTML += `<tr><td>${i+1}</td><td>${escapeHtml(b.name)}</td><td>${escapeHtml(b.restaurant)}</td><td>${escapeHtml(b.guests)}</td><td>${escapeHtml(b.time)}</td></tr>`;
  });

  // orders table
  const ordersTable = document.getElementById('orders-table');
  ordersTable.innerHTML = '';
  orders.forEach((o, i) => {
    ordersTable.innerHTML += `<tr><td>${i+1}</td><td>${escapeHtml(o.user)}</td><td>${escapeHtml(o.restaurant)}</td><td>${escapeHtml(o.dish)}</td><td>₹${escapeHtml(o.price)}</td></tr>`;
  });

  // charts
  renderCharts();
}

/* ---------------------------
   Charts (bookings by restaurant, bookings by time)
   Also show orders by restaurant on pie if you like - we'll do bookings pie and time bar.
   --------------------------- */
function renderCharts() {
  // bookings by restaurant
  const restCount = {};
  bookings.forEach(b => restCount[b.restaurant] = (restCount[b.restaurant]||0)+1);
  const restLabels = Object.keys(restCount);
  const restValues = Object.values(restCount);

  const pieCtx = document.getElementById('pieChart').getContext('2d');
  if (pieChartObj) pieChartObj.destroy();
  pieChartObj = new Chart(pieCtx, {
    type: 'pie',
    data: {
      labels: restLabels.length ? restLabels : ['No bookings'],
      datasets: [{
        data: restValues.length ? restValues : [1],
        backgroundColor: ['#ffbb00','#ff4b2b','#00c853','#42a5f5','#ab47bc','#ff7043']
      }]
    },
    options: {
      plugins: { title: { display: true, text: 'Bookings by Restaurant' } }
    }
  });

  // bookings by time (bar)
  const timeCount = {};
  bookings.forEach(b => timeCount[b.time] = (timeCount[b.time]||0)+1);
  const timeLabels = Object.keys(timeCount);
  const timeValues = Object.values(timeCount);

  const barCtx = document.getElementById('barChart').getContext('2d');
  if (barChartObj) barChartObj.destroy();
  barChartObj = new Chart(barCtx, {
    type: 'bar',
    data: {
      labels: timeLabels.length ? timeLabels : ['No bookings'],
      datasets: [{
        label: 'Bookings',
        data: timeValues.length ? timeValues : [0],
        backgroundColor: '#ffbb00'
      }]
    },
    options: {
      plugins: { title: { display: true, text: 'Bookings by Time' } },
      scales: { y: { beginAtZero: true, precision:0 } }
    }
  });
}

/* ---------------------------
   Export admin dashboard as PDF
   --------------------------- */
async function exportDashboardPDF() {
  const el = document.getElementById('admin-capture-area');
  const originalScale = document.body.style.transform;
  // use html2canvas to capture the admin area
  try {
    const canvas = await html2canvas(el, { scale: 2 });
    const imgData = canvas.toDataURL('image/png');
    const { jsPDF } = window.jspdf;
    const pdf = new jsPDF('p', 'mm', 'a4');
    const pageWidth = pdf.internal.pageSize.getWidth();
    const pageHeight = pdf.internal.pageSize.getHeight();
    // fit image into PDF with margins
    const imgProps = pdf.getImageProperties(imgData);
    const imgWidth = pageWidth - 20;
    const imgHeight = (imgProps.height * imgWidth) / imgProps.width;
    let y = 10;
    pdf.addImage(imgData, 'PNG', 10, y, imgWidth, imgHeight);
    pdf.save('FindNearby_admin_dashboard.pdf');
  } catch (e) {
    alert('Failed to export PDF: ' + e.message);
  } finally {
    document.body.style.transform = originalScale;
  }
}

/* ---------------------------
   Helpers & small UI
   --------------------------- */
function showModal(html) {
  document.getElementById('modal-content').innerHTML = html;
  document.getElementById('modal').style.display = 'flex';
}
function closeModal() {
  document.getElementById('modal').style.display = 'none';
}
function escapeHtml(s) {
  if (s == null) return '';
  return String(s).replaceAll('&','&amp;').replaceAll('<','&lt;').replaceAll('>','&gt;').replaceAll('"','&quot;');
}

/* ---------------------------
   Initialize: if users/bookings/orders present, admin can view them
   --------------------------- */
(function init() {
  // show login by default
  document.querySelectorAll('section').forEach(s => s.style.display = 'none');
  document.getElementById('login').style.display = 'flex';
  // if user was previously logged in (not admin) optionally restore? we'll keep logged out for safety
  updateAdminDashboard();
})();

/* ---------------------------
   Keyboard/UX niceties (press Enter to login)
   --------------------------- */
document.getElementById('password').addEventListener('keydown', (e) => {
  if (e.key === 'Enter') login();
});
document.getElementById('username').addEventListener('keydown', (e) => {
  if (e.key === 'Enter') login();
});
</script>

</body>
</html>
