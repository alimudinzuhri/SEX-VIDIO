# SEX-VIDIO
Tentu, ini adalah solusi lengkap untuk **Website Penjualan Produk Digital** dalam satu file HTML.

Website ini dirancang sebagai *Single Page Application* (SPA) yang mencakup:
1.  **Halaman Depan (Storefront):** Katalog produk, pencarian, filter kategori, dan keranjang belanja.
2.  **Sistem Keranjang & Checkout:** Simulasi pembayaran dan "download" produk digital.
3.  **Dashboard Admin:** Panel untuk mengelola produk (Tambah, Edit, Hapus) dan melihat statistik penjualan sederhana.
4.  **Penyimpanan Data:** Menggunakan `localStorage` browser, sehingga data produk dan pesanan tidak hilang saat halaman di-refresh.

Silakan simpan kode berikut sebagai file `.html` (contoh: `toko-digital.html`) dan buka di browser Anda.

```html
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DigiStore - Toko Produk Digital</title>
    <style>
        :root {
            --primary: #4F46E5;
            --primary-dark: #4338ca;
            --secondary: #64748b;
            --bg-light: #f8fafc;
            --white: #ffffff;
            --danger: #ef4444;
            --success: #10b981;
            --text-dark: #1e293b;
            --text-light: #64748b;
            --border: #e2e8f0;
            --shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1);
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
        }

        body {
            background-color: var(--bg-light);
            color: var(--text-dark);
            display: flex;
            flex-direction: column;
            min-height: 100vh;
        }

        /* --- Components --- */
        button {
            cursor: pointer;
            border: none;
            border-radius: 6px;
            padding: 8px 16px;
            font-weight: 600;
            transition: all 0.2s;
        }

        .btn-primary { background-color: var(--primary); color: white; }
        .btn-primary:hover { background-color: var(--primary-dark); }
        .btn-danger { background-color: var(--danger); color: white; }
        .btn-danger:hover { opacity: 0.9; }
        .btn-outline { background-color: transparent; border: 1px solid var(--border); color: var(--text-dark); }
        .btn-outline:hover { background-color: var(--border); }
        .btn-sm { padding: 4px 8px; font-size: 0.85rem; }

        input, select, textarea {
            width: 100%;
            padding: 10px;
            border: 1px solid var(--border);
            border-radius: 6px;
            margin-bottom: 10px;
            font-size: 14px;
        }

        input:focus, select:focus, textarea:focus {
            outline: 2px solid var(--primary);
            border-color: transparent;
        }

        /* --- Header & Nav --- */
        header {
            background-color: var(--white);
            box-shadow: var(--shadow);
            padding: 1rem 2rem;
            position: sticky;
            top: 0;
            z-index: 100;
        }

        nav {
            display: flex;
            justify-content: space-between;
            align-items: center;
            max-width: 1200px;
            margin: 0 auto;
        }

        .logo {
            font-size: 1.5rem;
            font-weight: bold;
            color: var(--primary);
            text-decoration: none;
        }

        .nav-links {
            display: flex;
            gap: 20px;
            align-items: center;
        }

        .nav-item {
            color: var(--text-dark);
            text-decoration: none;
            font-weight: 500;
            position: relative;
        }

        .nav-item:hover { color: var(--primary); }
        
        .cart-badge {
            background-color: var(--danger);
            color: white;
            border-radius: 50%;
            padding: 2px 6px;
            font-size: 0.7rem;
            position: absolute;
            top: -8px;
            right: -10px;
        }

        /* --- Main Layout --- */
        main {
            flex: 1;
            max-width: 1200px;
            width: 100%;
            margin: 2rem auto;
            padding: 0 1rem;
        }

        .section-view { display: none; }
        .section-view.active { display: block; animation: fadeIn 0.3s ease; }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* --- Store Front --- */
        .hero {
            background: linear-gradient(135deg, var(--primary), #818cf8);
            color: white;
            padding: 2rem;
            border-radius: 12px;
            margin-bottom: 2rem;
            text-align: center;
        }

        .filters {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            flex-wrap: wrap;
        }

        .product-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
            gap: 20px;
        }

        .product-card {
            background: var(--white);
            border-radius: 8px;
            overflow: hidden;
            box-shadow: var(--shadow);
            transition: transform 0.2s;
            display: flex;
            flex-direction: column;
        }

        .product-card:hover { transform: translateY(-4px); }

        .product-img {
            width: 100%;
            height: 160px;
            object-fit: cover;
            background-color: #eee;
        }

        .product-info {
            padding: 15px;
            flex: 1;
            display: flex;
            flex-direction: column;
        }

        .product-category {
            font-size: 0.75rem;
            color: var(--text-light);
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-bottom: 5px;
        }

        .product-title {
            font-size: 1.1rem;
            font-weight: bold;
            margin-bottom: 5px;
            color: var(--text-dark);
        }

        .product-price {
            font-size: 1.2rem;
            color: var(--primary);
            font-weight: bold;
            margin-top: auto;
            margin-bottom: 10px;
        }

        /* --- Cart & Checkout --- */
        .cart-container {
            background: var(--white);
            padding: 2rem;
            border-radius: 12px;
            box-shadow: var(--shadow);
        }

        .cart-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 1rem 0;
            border-bottom: 1px solid var(--border);
        }

        .cart-total {
            text-align: right;
            font-size: 1.5rem;
            font-weight: bold;
            margin: 20px 0;
        }

        /* --- Admin Dashboard --- */
        .admin-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 2rem;
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
            margin-bottom: 2rem;
        }

        .stat-card {
            background: var(--white);
            padding: 1.5rem;
            border-radius: 8px;
            box-shadow: var(--shadow);
            border-left: 4px solid var(--primary);
        }

        .stat-value { font-size: 2rem; font-weight: bold; margin-top: 5px; }
        .stat-label { color: var(--text-light); font-size: 0.9rem; }

        .data-table {
            width: 100%;
            border-collapse: collapse;
            background: var(--white);
            border-radius: 8px;
            overflow: hidden;
            box-shadow: var(--shadow);
        }

        .data-table th, .data-table td {
            padding: 12px 15px;
            text-align: left;
            border-bottom: 1px solid var(--border);
        }

        .data-table th { background-color: #f1f5f9; font-weight: 600; }
        .data-table tr:hover { background-color: #f8fafc; }
        
        .action-btns { display: flex; gap: 5px; }

        /* --- Modal --- */
        .modal-overlay {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.5);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }
        
        .modal-overlay.open { display: flex; }

        .modal {
            background: var(--white);
            padding: 2rem;
            border-radius: 12px;
            width: 90%;
            max-width: 500px;
            position: relative;
            max-height: 90vh;
            overflow-y: auto;
        }

        .close-modal {
            position: absolute;
            top: 15px;
            right: 15px;
            background: none;
            font-size: 1.5rem;
            padding: 0;
            color: var(--text-light);
        }

        /* --- Toast Notification --- */
        #toast-container {
            position: fixed;
            bottom: 20px;
            right: 20px;
            z-index: 2000;
        }

        .toast {
            background: var(--text-dark);
            color: white;
            padding: 12px 20px;
            border-radius: 6px;
            margin-top: 10px;
            box-shadow: var(--shadow);
            animation: slideIn 0.3s ease;
            display: flex;
            align-items: center;
            justify-content: space-between;
            min-width: 250px;
        }

        .toast.success { border-left: 4px solid var(--success); }
        .toast.error { border-left: 4px solid var(--danger); }

        @keyframes slideIn {
            from { transform: translateX(100%); opacity: 0; }
            to { transform: translateX(0); opacity: 1; }
        }

        /* Empty state helper */
        .empty-state { text-align: center; padding: 40px; color: var(--text-light); }
    </style>
</head>
<body>

    <!-- Header -->
    <header>
        <nav>
            <a href="#" class="logo" onclick="app.navigate('home')">DigiStore</a>
            <div class="nav-links">
                <a href="#" class="nav-item" onclick="app.navigate('home')">Belanja</a>
                <a href="#" class="nav-item" onclick="app.navigate('cart')">Keranjang <span id="cart-count" class="cart-badge">0</span></a>
                <a href="#" class="nav-item" onclick="app.navigate('admin')">Admin Panel</a>
            </div>
        </nav>
    </header>

    <main>
        <!-- View: Home (Store) -->
        <section id="view-home" class="section-view active">
            <div class="hero">
                <h1>Aset Digital Premium</h1>
                <p>Temukan template e-book, software, dan desain grafis berkualitas tinggi.</p>
            </div>

            <div class="filters">
                <input type="text" id="search-input" placeholder="Cari produk..." style="max-width: 300px; margin-bottom: 0;">
                <select id="category-filter" style="max-width: 200px; margin-bottom: 0;">
                    <option value="all">Semua Kategori</option>
                    <option value="ebook">E-Book</option>
                    <option value="template">Template</option>
                    <option value="software">Software</option>
                    <option value="graphic">Grafis</option>
                </select>
            </div>

            <div id="product-list" class="product-grid">
                <!-- Products injected via JS -->
            </div>
        </section>

        <!-- View: Cart -->
        <section id="view-cart" class="section-view">
            <h2>Keranjang Belanja</h2>
            <div class="cart-container">
                <div id="cart-items">
                    <!-- Cart items injected via JS -->
                </div>
                <div class="cart-total">
                    Total: <span id="cart-total-price">Rp 0</span>
                </div>
                <div style="text-align: right;">
                    <button class="btn-outline" onclick="app.navigate('home')">Lanjut Belanja</button>
                    <button class="btn-primary" onclick="app.checkout()">Checkout Sekarang</button>
                </div>
            </div>
        </section>

        <!-- View: Admin Dashboard -->
        <section id="view-admin" class="section-view">
            <div class="admin-header">
                <h2>Dashboard Admin</h2>
                <button class="btn-primary" onclick="admin.openModal()">+ Tambah Produk</button>
            </div>

            <!-- Stats -->
            <div class="stats-grid">
                <div class="stat-card">
                    <div class="stat-label">Total Produk</div>
                    <div class="stat-value" id="stat-products">0</div>
                </div>
                <div class="stat-card">
                    <div class="stat-label">Total Penjualan</div>
                    <div class="stat-value" id="stat-sales">Rp 0</div>
                </div>
                <div class="stat-card">
                    <div class="stat-label">Pesanan</div>
                    <div class="stat-value" id="stat-orders">0</div>
                </div>
            </div>

            <!-- Product Table -->
            <h3>Daftar Produk</h3>
            <div style="overflow-x: auto; margin-top: 1rem;">
                <table class="data-table">
                    <thead>
                        <tr>
                            <th>ID</th>
                            <th>Produk</th>
                            <th>Kategori</th>
                            <th>Harga</th>
                            <th>Aksi</th>
                        </tr>
                    </thead>
                    <tbody id="admin-product-table">
                        <!-- Table rows injected via JS -->
                    </tbody>
                </table>
            </div>
        </section>
    </main>

    <!-- Modal: Add/Edit Product -->
    <div id="product-modal" class="modal-overlay">
        <div class="modal">
            <button class="close-modal" onclick="admin.closeModal()">&times;</button>
            <h2 id="modal-title" style="margin-bottom: 20px;">Tambah Produk</h2>
            <form id="product-form" onsubmit="admin.saveProduct(event)">
                <input type="hidden" id="prod-id">
                
                <label>Nama Produk</label>
                <input type="text" id="prod-name" required>
                
                <label>Kategori</label>
                <select id="prod-category" required>
                    <option value="ebook">E-Book</option>
                    <option value="template">Template</option>
                    <option value="software">Software</option>
                    <option value="graphic">Grafis</option>
                </select>

                <label>Harga (IDR)</label>
                <input type="number" id="prod-price" required min="0">

                <label>URL Gambar (Opsional)</label>
                <input type="text" id="prod-image" placeholder="Kosongkan untuk gambar acak">

                <label>Deskripsi</label>
                <textarea id="prod-desc" rows="3" required></textarea>

                <button type="submit" class="btn-primary" style="width: 100%;">Simpan Produk</button>
            </form>
        </div>
    </div>

    <!-- Modal: Product Details -->
    <div id="detail-modal" class="modal-overlay">
        <div class="modal">
            <button class="close-modal" onclick="app.closeDetailModal()">&times;</button>
            <div id="detail-content"></div>
            <div style="margin-top: 20px;">
                <button class="btn-primary" style="width: 100%;" id="detail-add-btn">Tambah ke Keranjang</button>
            </div>
        </div>
    </div>

    <!-- Toast Container -->
    <div id="toast-container"></div>

    <script>
        // --- DATA MANAGEMENT ---
        const defaultProducts = [
            { id: 1, name: "Panduan Belajar JavaScript", category: "ebook", price: 150000, desc: "E-book lengkap menguasai JS dari dasar hingga mahir.", img: "https://picsum.photos/seed/js/300/200" },
            { id: 2, name: "UI Kit Modern Website", category: "template", price: 250000, desc: "Template HTML/CSS siap pakai untuk landing page bisnis.", img: "https://picsum.photos/seed/ui/300/200" },
            { id: 3, name: "Invoice Generator Pro", category: "software", price: 500000, desc: "Aplikasi desktop sederhana untuk membuat faktur.", img: "https://picsum.photos/seed/inv/300/200" },
            { id: 4, name: "Pack Icon 3D", category: "graphic", price: 75000, desc: "Koleksi 100 icon 3D format PNG dan SVG.", img: "https://picsum.photos/seed/icon/300/200" }
        ];

        const Storage = {
            getProducts: () => JSON.parse(localStorage.getItem('ds_products')) || defaultProducts,
            setProducts: (data) => localStorage.setItem('ds_products', JSON.stringify(data)),
            getCart: () => JSON.parse(localStorage.getItem('ds_cart')) || [],
            setCart: (data) => localStorage.setItem('ds_cart', JSON.stringify(data)),
            getStats: () => JSON.parse(localStorage.getItem('ds_stats')) || { sales: 0, orders: 0 },
            setStats: (data) => localStorage.setItem('ds_stats', JSON.stringify(data)),
        };

        // Initialize if empty
        if (!localStorage.getItem('ds_products')) Storage.setProducts(defaultProducts);

        // --- UTILS ---
        const formatRupiah = (num) => {
            return new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR', minimumFractionDigits: 0 }).format(num);
        };

        const showToast = (message, type = 'success') => {
            const container = document.getElementById('toast-container');
            const toast = document.createElement('div');
            toast.className = `toast ${type}`;
            toast.innerHTML = `<span>${message}</span>`;
            container.appendChild(toast);
            setTimeout(() => toast.remove(), 3000);
        };

        // --- CORE APP ---
        const app = {
            products: [],
            cart: [],
            
            init: () => {
                app.products = Storage.getProducts();
                app.cart = Storage.getCart();
                app.renderProducts();
                app.updateCartCount();
                
                // Event Listeners for filters
                document.getElementById('search-input').addEventListener('input', app.renderProducts);
                document.getElementById('category-filter').addEventListener('change', app.renderProducts);
            },

            navigate: (viewId) => {
                document.querySelectorAll('.section-view').forEach(el => el.classList.remove('active'));
                document.getElementById(`view-${viewId}`).classList.add('active');
                
                if (viewId === 'cart') app.renderCart();
                if (viewId === 'admin') admin.init();
                window.scrollTo(0, 0);
            },

            renderProducts: () => {
                const search = document.getElementById('search-input').value.toLowerCase();
                const category = document.getElementById('category-filter').value;
                
                const container = document.getElementById('product-list');
                container.innerHTML = '';

                const filtered = app.products.filter(p => {
                    const matchSearch = p.name.toLowerCase().includes(search);
                    const matchCat = category === 'all' || p.category === category;
                    return matchSearch && matchCat;
                });

                if (filtered.length === 0) {
                    container.innerHTML = '<div class="empty-state" style="grid-column: 1/-1;">Tidak ada produk ditemukan.</div>';
                    return;
                }

                filtered.forEach(p => {
                    const card = document.createElement('div');
                    card.className = 'product-card';
                    card.innerHTML = `
                        <img src="${p.img}" alt="${p.name}" class="product-img" onclick="app.showDetail(${p.id})">
                        <div class="product-info">
                            <div class="product-category">${p.category}</div>
                            <div class="product-title" onclick="app.showDetail(${p.id})" style="cursor:pointer">${p.name}</div>
                            <div class="product-price">${formatRupiah(p.price)}</div>
                            <button class="btn-primary btn-sm" onclick="app.addToCart(${p.id})">Tambah ke Keranjang</button>
                        </div>
                    `;
                    container.appendChild(card);
                });
            },

            showDetail: (id) => {
                const product = app.products.find(p => p.id === id);
                if (!product) return;

                const content = document.getElementById('detail-content');
                content.innerHTML = `
                    <img src="${product.img}" style="width:100%; height:200px; object-fit:cover; border-radius:8px; margin-bottom:15px;">
                    <h3>${product.name}</h3>
                    <p style="color:var(--primary); font-weight:bold; font-size:1.2rem; margin:5px 0;">${formatRupiah(product.price)}</p>
                    <p style="color:var(--text-light); line-height:1.5;">${product.desc}</p>
                `;
                
                const btn = document.getElementById('detail-add-btn');
                btn.onclick = () => { app.addToCart(product.id); app.closeDetailModal(); };
                
                document.getElementById('detail-modal').classList.add('open');
            },

            closeDetailModal: () => {
                document.getElementById('detail-modal').classList.remove('open');
            },

            addToCart: (id) => {
                const existing = app.cart.find(item => item.id === id);
                if (existing) {
                    existing.qty++;
                } else {
                    app.cart.push({ id: id, qty: 1 });
                }
                app.updateCart();
                showToast('Produk ditambahkan ke keranjang!');
            },

            removeFromCart: (id) => {
                app.cart = app.cart.filter(item => item.id !== id);
                app.updateCart();
                app.renderCart(); // Re-render cart view if open
            },

            updateCart: () => {
                Storage.setCart(app.cart);
                app.updateCartCount();
            },

            updateCartCount: () => {
                const totalQty = app.cart.reduce((sum, item) => sum + item.qty, 0);
                document.getElementById('cart-count').innerText = totalQty;
            },

            renderCart: () => {
                const container = document.getElementById('cart-items');
                container.innerHTML = '';
                let total = 0;

                if (app.cart.length === 0) {
                    container.innerHTML = '<div class="empty-state">Keranjang Anda kosong.</div>';
                    document.getElementById('cart-total-price').innerText = formatRupiah(0);
                    return;
                }

                app.cart.forEach(item => {
                    const product = app.products.find(p => p.id === item.id);
                    if (!product) return;

                    const itemTotal = product.price * item.qty;
                    total += itemTotal;

                    const div = document.createElement('div');
                    div.className = 'cart-item';
                    div.innerHTML = `
                        <div>
                            <strong>${product.name}</strong><br>
                            <small>${formatRupiah(product.price)} x ${item.qty}</small>
                        </div>
                        <div style="text-align:right;">
                            <div>${formatRupiah(itemTotal)}</div>
                            <button class="btn-danger btn-sm" style="margin-top:5px;" onclick="app.removeFromCart(${item.id})">Hapus</button>
                        </div>
                    `;
                    container.appendChild(div);
                });

                document.getElementById('cart-total-price').innerText = formatRupiah(total);
            },

            checkout: () => {
                if (app.cart.length === 0) {
                    showToast('Keranjang kosong!', 'error');
                    return;
                }

                // Calculate total
                let total = 0;
                app.cart.forEach(item => {
                    const product = app.products.find(p => p.id === item.id);
                    if (product) total += product.price * item.qty;
                });

                // Update stats
                const stats = Storage.getStats();
                stats.sales += total;
                stats.orders += 1;
                Storage.setStats(stats);

                // Simulate Process
                showToast('Memproses pembayaran...');
                setTimeout(() => {
                    app.cart = [];
                    app.updateCart();
                    app.navigate('home');
                    showToast(`Pembelian berhasil! Total: ${formatRupiah(total)}. Cek email untuk download.`, 'success');
                    
                    // Create fake download experience
                    console.log("Order created. Simulation over.");
                }, 1500);
            }
        };

        // --- ADMIN LOGIC ---
        const admin = {
            init: () => {
                const products = Storage.getProducts();
                const stats = Storage.getStats();
                
                // Render Stats
                document.getElementById('stat-products').innerText = products.length;
                document.getElementById('stat-sales').innerText = formatRupiah(stats.sales);
                document.getElementById('stat-orders').innerText = stats.orders;

                // Render Table
                const tbody = document.getElementById('admin-product-table');
                tbody.innerHTML = '';
                
                products.forEach(p => {
                    const tr = document.createElement('tr');
                    tr.innerHTML = `
                        <td>#${p.id}</td>
                        <td>
                            <div style="display:flex; align-items:center; gap:10px;">
                                <img src="${p.img}" style="width:30px; height:30px; border-radius:4px; object-fit:cover;">
                                ${p.name}
                            </div>
                        </td>
                        <td><span style="background:#e0e7ff; color:var(--primary); padding:2px 6px; border-radius:4px; font-size:0.8rem;">${p.category}</span></td>
                        <td>${formatRupiah(p.price)}</td>
                        <td>
                            <div class="action-btns">
                                <button class="btn-outline btn-sm" onclick="admin.editProduct(${p.id})">Edit</button>
                                <button class="btn-danger btn-sm" onclick="admin.deleteProduct(${p.id})">Hapus</button>
                            </div>
                        </td>
                    `;
                    tbody.appendChild(tr);
                });
            },

            openModal: (isEdit = false) => {
                document.getElementById('product-modal').classList.add('open');
                document.getElementById('modal-title').innerText = isEdit ? 'Edit Produk' : 'Tambah Produk';
                if (!isEdit) {
                    document.getElementById('product-form').reset();
                    document.getElementById('prod-id').value = '';
                }
            },

            closeModal: () => {
                document.getElementById('product-modal').classList.remove('open');
            },

            saveProduct: (e) => {
                e.preventDefault();
                const id = document.getElementById('prod-id').value;
                const name = document.getElementById('prod-name').value;
                const category = document.getElementById('prod-category').value;
                const price = parseInt(document.getElementById('prod-price').value);
                const desc = document.getElementById('prod-desc').value;
                let img = document.getElementById('prod-image').value;

                if (!img) {
                    // Generate random image based on name seed if empty
                    img = `https://picsum.photos/seed/${name.replace(/\s/g,'')}/300/200`;
                }

                let products = Storage.getProducts();

                if (id) {
                    // Edit existing
                    const index = products.findIndex(p => p.id == id);
                    if (index !== -1) {
                        products[index] = { ...products[index], name, category, price, desc, img };
                        showToast('Produk berhasil diperbarui');
                    }
                } else {
                    // Create new
                    const newId = products.length > 0 ? Math.max(...products.map(p => p.id)) + 1 : 1;
                    products.push({ id: newId, name, category, price, desc, img });
                    showToast('Produk berhasil ditambahkan');
                }

                Storage.setProducts(products);
                app.products = products; // Sync main app
                admin.closeModal();
                admin.init(); // Re-render admin
                app.renderProducts(); // Re-render store
            },

            editProduct: (id) => {
                const product = Storage.getProducts().find(p => p.id === id);
                if (!product) return;

                document.getElementById('prod-id').value = product.id;
                document.getElementById('prod-name').value = product.name;
                document.getElementById('prod-category').value = product.category;
                document.getElementById('prod-price').value = product.price;
                document.getElementById('prod-image').value = product.img;
                document.getElementById('prod-desc').value = product.desc;

                admin.openModal(true);
            },

            deleteProduct: (id) => {
                if (confirm('Apakah Anda yakin ingin menghapus produk ini?')) {
                    let products = Storage.getProducts();
                    products = products.filter(p => p.id !== id);
                    Storage.setProducts(products);
                    app.products = products;
                    admin.init();
                    app.renderProducts();
                    showToast('Produk dihapus', 'error');
                }
            }
        };

        // Start App
        app.init();

    </script>
</body>
</html>
```

### Fitur-fitur yang tersedia:

1.  **Halaman Belanja (Storefront):**
    *   **Katalog Produk:** Menampilkan produk dalam layout grid yang responsif.
    *   **Pencarian & Filter:** Anda bisa mengetik nama produk di kolom pencarian atau memfilter berdasarkan kategori (E-book, Template, dll).
    *   **Detail Produk:** Klik pada gambar atau nama produk untuk melihat deskripsi lengkap dalam bentuk *popup modal*.
    *   **Keranjang:** Tombol untuk menambah item dan melihat total item di navigasi atas.

2.  **Manajemen Keranjang & Checkout:**
    *   **Edit Keranjang:** Melihat item yang dipilih, menghapus item, dan melihat total harga.
    *   **Simulasi Checkout:** Saat tombol "Checkout" ditekan, sistem akan memvalidasi, memproses pembayaran (simulasi delay), mengosongkan keranjang, dan memperbarui statistik penjualan di admin.

3.  **Dashboard Admin:**
    *   Akses melalui menu "Admin Panel" di pojok kanan atas.
    *   **Statistik:** Menampilkan total produk aktif, total uang masuk (dari penjualan), dan jumlah pesanan yang berhasil.
    *   **Tabel Produk:** Daftar semua produk dengan aksi Edit dan Hapus.
    *   **Tambah Produk:** Formulir modal untuk menambah produk baru. Jika URL gambar dikosongkan, sistem akan otomatis membuat gambar *placeholder*.
    *   **Edit/Hapus:** Mengubah data produk yang sudah ada atau menghapusnya secara permanen.

4.  **Teknis & UX:**
    *   **Tanpa Refresh:** Berpindah halaman (Home, Cart, Admin) terasa instan.
    *   **Notifikasi (Toast):** Muncul di pojok kanan bawah saat aksi berhasil (misal: "Produk ditambahkan").
    *   **Persistent Data:** Coba tambahkan produk atau lakukan checkout, lalu *refresh* browser Anda. Data akan tetap tersimpan (menggunakan LocalStorage).
