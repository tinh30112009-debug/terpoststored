# terpoststored
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>🛒 MyShop - Thanh toán QR & Zalo</title>

    <!-- Thêm thư viện QRCode.js từ CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js">
    </script>

    <style>
        /* ===== RESET & BASE ===== */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Arial, sans-serif;
        }

        body {
            background: #f0f2f5;
            padding-bottom: 100px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 0 20px;
        }

        /* ===== HEADER ===== */
        .header {
            background: linear-gradient(135deg, #1a73e8, #0d47a1);
            color: white;
            padding: 18px 0;
            position: sticky;
            top: 0;
            z-index: 100;
            box-shadow: 0 2px 15px rgba(26, 115, 232, 0.3);
        }

        .header .container {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 10px;
        }

        .logo {
            font-size: 26px;
            font-weight: 700;
        }

        .logo span {
            color: #ffd700;
        }

        .header-right {
            display: flex;
            align-items: center;
            gap: 15px;
            flex-wrap: wrap;
        }

        .header-right .btn {
            padding: 10px 20px;
            border-radius: 30px;
            border: none;
            font-weight: 600;
            cursor: pointer;
            transition: 0.3s;
            font-size: 15px;
        }

        .btn-zalo {
            background: #0068ff;
            color: white;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .btn-zalo:hover {
            background: #0055cc;
            transform: scale(1.05);
        }

        .btn-cart {
            background: rgba(255, 255, 255, 0.2);
            color: white;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .btn-cart:hover {
            background: rgba(255, 255, 255, 0.3);
        }

        .cart-badge {
            background: #ffd700;
            color: #1a73e8;
            padding: 2px 10px;
            border-radius: 20px;
            font-weight: 700;
            margin-left: 5px;
        }

        /* ===== BANNER ===== */
        .banner {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 40px 0;
            text-align: center;
            margin-bottom: 30px;
        }

        .banner h2 {
            font-size: 30px;
            margin-bottom: 10px;
        }

        .banner .highlight {
            background: rgba(255, 215, 0, 0.2);
            padding: 8px 20px;
            border-radius: 30px;
            display: inline-block;
            margin-top: 10px;
            border: 1px solid rgba(255, 215, 0, 0.3);
        }

        /* ===== SẢN PHẨM ===== */
        .product-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
            gap: 25px;
            margin-top: 20px;
        }

        .product-card {
            background: white;
            border-radius: 16px;
            padding: 18px;
            text-align: center;
            transition: all 0.3s;
            box-shadow: 0 2px 12px rgba(0, 0, 0, 0.06);
            border: 1px solid rgba(0, 0, 0, 0.04);
            position: relative;
        }

        .product-card:hover {
            transform: translateY(-6px);
            box-shadow: 0 12px 35px rgba(26, 115, 232, 0.12);
        }

        .product-card .badge {
            position: absolute;
            top: 12px;
            left: 12px;
            background: #e53935;
            color: white;
            padding: 4px 14px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: 700;
        }

        .product-card img {
            width: 100%;
            height: 200px;
            object-fit: cover;
            border-radius: 10px;
            background: #f0f2f5;
        }

        .product-card h3 {
            margin: 14px 0 8px;
            font-size: 17px;
            color: #1a1a2e;
        }

        .product-card .price {
            color: #e53935;
            font-size: 20px;
            font-weight: 700;
        }

        .product-card .price .old-price {
            color: #999;
            font-size: 14px;
            text-decoration: line-through;
            font-weight: 400;
            margin-left: 10px;
        }

        .product-card .rating {
            color: #ffd700;
            margin: 8px 0 12px;
        }

        .product-card button {
            background: linear-gradient(135deg, #1a73e8, #0d47a1);
            color: white;
            border: none;
            padding: 12px 20px;
            border-radius: 30px;
            cursor: pointer;
            font-size: 15px;
            font-weight: 600;
            transition: 0.3s;
            width: 100%;
        }

        .product-card button:hover {
            transform: scale(1.02);
            box-shadow: 0 4px 20px rgba(26, 115, 232, 0.4);
        }

        .product-card button.added {
            background: #4caf50;
        }

        /* ===== POPUP GIỎ HÀNG ===== */
        .overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            backdrop-filter: blur(4px);
            display: none;
            z-index: 999;
        }

        .overlay.active {
            display: block;
        }

        .popup {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: white;
            padding: 35px;
            border-radius: 20px;
            box-shadow: 0 30px 80px rgba(0, 0, 0, 0.3);
            width: 92%;
            max-width: 550px;
            max-height: 90vh;
            z-index: 1000;
            display: none;
            overflow-y: auto;
        }

        .popup.active {
            display: block;
            animation: slideUp 0.3s ease;
        }

        @keyframes slideUp {
            from {
                transform: translate(-50%, 70%);
                opacity: 0;
            }
            to {
                transform: translate(-50%, -50%);
                opacity: 1;
            }
        }

        .popup-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            padding-bottom: 15px;
            border-bottom: 2px solid #f0f2f5;
        }

        .popup-header h3 {
            font-size: 22px;
            color: #1a1a2e;
        }

        .popup-header .close-btn {
            background: none;
            border: none;
            font-size: 28px;
            cursor: pointer;
            color: #999;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: 0.3s;
        }

        .popup-header .close-btn:hover {
            background: #f0f2f5;
        }

        .cart-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 12px 0;
            border-bottom: 1px solid #f0f2f5;
        }

        .cart-item .item-info {
            flex: 1;
        }

        .cart-item .item-name {
            font-weight: 600;
            color: #1a1a2e;
        }

        .cart-item .item-price {
            color: #e53935;
            font-weight: 700;
        }

        .cart-item .qty-control {
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .cart-item .qty-control button {
            width: 30px;
            height: 30px;
            border-radius: 50%;
            border: 1px solid #ddd;
            background: white;
            cursor: pointer;
            font-size: 16px;
            transition: 0.3s;
        }

        .cart-item .qty-control button:hover {
            background: #1a73e8;
            color: white;
            border-color: #1a73e8;
        }

        .cart-total {
            border-top: 2px solid #f0f2f5;
            padding-top: 18px;
            margin-top: 15px;
            display: flex;
            justify-content: space-between;
            font-size: 20px;
            font-weight: 700;
        }

        .cart-total span {
            color: #e53935;
            font-size: 24px;
        }

        .popup .btn-checkout {
            width: 100%;
            padding: 16px;
            background: linear-gradient(135deg, #1a73e8, #0d47a1);
            color: white;
            border: none;
            border-radius: 30px;
            font-size: 18px;
            font-weight: 700;
            cursor: pointer;
            margin-top: 18px;
            transition: 0.3s;
        }

        .popup .btn-checkout:hover {
            transform: scale(1.02);
            box-shadow: 0 4px 25px rgba(26, 115, 232, 0.4);
        }

        .popup .btn-clear {
            width: 100%;
            padding: 12px;
            background: none;
            color: #e53935;
            border: 2px solid #e53935;
            border-radius: 30px;
            font-size: 15px;
            font-weight: 600;
            cursor: pointer;
            margin-top: 10px;
            transition: 0.3s;
        }

        .popup .btn-clear:hover {
            background: #e53935;
            color: white;
        }

        /* ===== FORM THANH TOÁN ===== */
        .checkout-form {
            display: none;
            margin-top: 30px;
            padding: 30px;
            background: white;
            border-radius: 16px;
            box-shadow: 0 2px 12px rgba(0, 0, 0, 0.06);
        }

        .checkout-form.active {
            display: block;
        }

        .checkout-form h3 {
            font-size: 24px;
            color: #1a1a2e;
            margin-bottom: 20px;
            border-bottom: 2px solid #f0f2f5;
            padding-bottom: 15px;
        }

        .form-group {
            margin-bottom: 18px;
        }

        .form-group label {
            display: block;
            font-weight: 600;
            margin-bottom: 6px;
            color: #333;
        }

        .form-group input,
        .form-group textarea,
        .form-group select {
            width: 100%;
            padding: 12px 16px;
            border: 2px solid #e0e0e0;
            border-radius: 10px;
            font-size: 15px;
            transition: 0.3s;
            background: #fafafa;
        }

        .form-group input:focus,
        .form-group textarea:focus,
        .form-group select:focus {
            border-color: #1a73e8;
            outline: none;
            background: white;
            box-shadow: 0 0 0 4px rgba(26, 115, 232, 0.1);
        }

        .form-group textarea {
            resize: vertical;
            min-height: 80px;
        }

        .form-row {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
        }

        /* ===== QR CODE SECTION ===== */
        .qr-section {
            text-align: center;
            padding: 30px 20px;
            background: #f8f9fa;
            border-radius: 16px;
            margin-top: 20px;
        }

        .qr-section h4 {
            font-size: 20px;
            color: #1a1a2e;
            margin-bottom: 10px;
        }

        .qr-section .qr-container {
            display: flex;
            justify-content: center;
            margin: 20px 0;
            padding: 20px;
            background: white;
            border-radius: 16px;
            box-shadow: 0 2px 12px rgba(0, 0, 0, 0.06);
        }

        .qr-section .qr-container #qrcode {
            display: inline-block;
        }

        .qr-section .qr-container #qrcode img {
            display: block;
            margin: 0 auto;
        }

        .qr-section .qr-info {
            font-size: 16px;
            color: #555;
        }

        .qr-section .qr-info strong {
            color: #1a73e8;
        }

        .qr-section .copy-btn {
            padding: 10px 24px;
            background: #1a73e8;
            color: white;
            border: none;
            border-radius: 30px;
            cursor: pointer;
            font-weight: 600;
            margin-top: 10px;
            transition: 0.3s;
        }

        .qr-section .copy-btn:hover {
            background: #0d47a1;
        }

        /* ===== NÚT ZALO CỐ ĐỊNH ===== */
        .zalo-float {
            position: fixed;
            bottom: 30px;
            left: 30px;
            z-index: 50;
        }

        .zalo-float a {
            display: flex;
            align-items: center;
            gap: 10px;
            background: #0068ff;
            color: white;
            padding: 14px 24px;
            border-radius: 50px;
            text-decoration: none;
            font-weight: 700;
            box-shadow: 0 6px 30px rgba(0, 104, 255, 0.4);
            transition: 0.3s;
            font-size: 16px;
        }

        .zalo-float a:hover {
            transform: translateY(-3px) scale(1.03);
            box-shadow: 0 10px 40px rgba(0, 104, 255, 0.5);
        }

        .zalo-float .zalo-icon {
            font-size: 28px;
        }

        /* ===== TOAST ===== */
        #toast {
            position: fixed;
            bottom: 100px;
            left: 50%;
            transform: translateX(-50%);
            background: #1a1a2e;
            color: white;
            padding: 14px 30px;
            border-radius: 50px;
            font-size: 15px;
            box-shadow: 0 6px 30px rgba(0, 0, 0, 0.3);
            z-index: 2000;
            display: none;
            text-align: center;
            max-width: 90%;
        }

        /* ===== FOOTER ===== */
        .footer {
            background: #1a1a2e;
            color: #aaa;
            padding: 30px 0;
            margin-top: 40px;
            text-align: center;
        }

        .footer .container {
            display: flex;
            justify-content: space-between;
            flex-wrap: wrap;
            gap: 20px;
        }

        .footer .footer-col h4 {
            color: white;
            margin-bottom: 12px;
        }

        .footer .footer-col p {
            font-size: 14px;
            line-height: 1.8;
        }

        /* ===== RESPONSIVE ===== */
        @media (max-width: 768px) {
            .header .container {
                flex-direction: column;
                text-align: center;
            }

            .form-row {
                grid-template-columns: 1fr;
            }

            .product-grid {
                grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
                gap: 15px;
            }

            .popup {
                padding: 20px;
                width: 95%;
            }

            .zalo-float a {
                padding: 10px 16px;
                font-size: 14px;
            }

            .zalo-float .zalo-icon {
                font-size: 22px;
            }

            .footer .container {
                flex-direction: column;
                text-align: center;
            }
        }

        @media (max-width: 480px) {
            .product-grid {
                grid-template-columns: 1fr 1fr;
                gap: 10px;
            }

            .product-card {
                padding: 12px;
            }

            .product-card img {
                height: 140px;
            }

            .product-card h3 {
                font-size: 14px;
            }

            .product-card .price {
                font-size: 16px;
            }

            .product-card button {
                font-size: 13px;
                padding: 10px;
            }

            .banner h2 {
                font-size: 22px;
            }
        }
    </style>
</head>
<body>

    <!-- ===== HEADER ===== -->
    <header class="header">
        <div class="container">
            <div class="logo">🛒 <span>My</span>Shop</div>
            <div class="header-right">
                <button class="btn btn-zalo" onclick="openZalo()">
                    💬 Zalo
                </button>
                <button class="btn btn-cart" onclick="openCart()">
                    🛍️ Giỏ hàng <span class="cart-badge" id="cart-badge">0</span>
                </button>
            </div>
        </div>
    </header>

    <!-- ===== BANNER ===== -->
    <section class="banner">
        <div class="container">
            <h2>🔥 Mua sắm dễ dàng - Thanh toán qua QR</h2>
            <p>Quét mã QR để thanh toán nhanh chóng qua ngân hàng</p>
            <div class="highlight">💳 Hỗ trợ Vietcombank, Techcombank, Momo, ZaloPay</div>
        </div>
    </section>

    <!-- ===== DANH SÁCH SẢN PHẨM ===== -->
    <section class="container">
        <h2 style="font-size:28px;color:#1a1a2e;margin-bottom:20px;">📦 Sản phẩm của chúng tôi</h2>
        <div class="product-grid" id="product-list">
            <!-- JavaScript render -->
        </div>
    </section>

    <!-- ===== FORM THANH TOÁN ===== -->
    <div class="container">
        <div class="checkout-form" id="checkout-form">
            <h3>💳 Thông tin thanh toán</h3>
            <form id="orderForm">
                <div class="form-row">
                    <div class="form-group">
                        <label>Họ và tên *</label>
                        <input type="text" id="fullName" placeholder="Nguyễn Văn A" required />
                    </div>
                    <div class="form-group">
                        <label>Số điện thoại *</label>
                        <input type="tel" id="phone" placeholder="0901234567" required />
                    </div>
                </div>
                <div class="form-group">
                    <label>Email</label>
                    <input type="email" id="email" placeholder="example@email.com" />
                </div>
                <div class="form-group">
                    <label>Địa chỉ giao hàng *</label>
                    <input type="text" id="address" placeholder="Số nhà, đường, quận/huyện" required />
                </div>
                <div class="form-row">
                    <div class="form-group">
                        <label>Tỉnh/Thành phố</label>
                        <select id="province">
                            <option value="Hà Nội">Hà Nội</option>
                            <option value="TP.HCM">TP.HCM</option>
                            <option value="Đà Nẵng">Đà Nẵng</option>
                            <option value="Hải Phòng">Hải Phòng</option>
                            <option value="Cần Thơ">Cần Thơ</option>
                            <option value="Bình Dương">Bình Dương</option>
                            <option value="Đồng Nai">Đồng Nai</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Phương thức thanh toán</label>
                        <select id="paymentMethod">
                            <option value="qr">Quét mã QR (Ngân hàng)</option>
                            <option value="momo">MoMo</option>
                            <option value="zalopay">ZaloPay</option>
                            <option value="cod">Thanh toán khi nhận hàng (COD)</option>
                
