<script>
        // الرابط الصحيح الخاص بك تم وضعه هنا في المكان المخصص
        const SHEET_CSV_URL = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vQasJBnMBne7WXm6Ws2oIvyNCDLkNEyoSlh0HAusWQmsrOb2uxbRpMs7isgFscSsNzoDShmtWrAiGcc/pub?output=csv'; 

        let cart = [];
        let total = 0;

        async function loadProducts() {
            try {
                const response = await fetch(SHEET_CSV_URL);
                const data = await response.text();
                const rows = data.split('\n').slice(1); 
                
                const container = document.getElementById('products-container');
                container.innerHTML = '';

                rows.forEach(row => {
                    // استخدام الفاصلة لقراءة الأعمدة
                    const columns = row.split(',');
                    if (columns.length >= 3) {
                        const name = columns[0];
                        const desc = columns[1];
                        const price = columns[2];
                        
                        container.innerHTML += `
                            <div class="product-card">
                                <h3>${name}</h3>
                                <p>${desc}</p>
                                <span class="price-tag">${price.trim()}$</span>
                                <button class="btn-add" onclick="addToCart('${name.trim()}', ${parseFloat(price)})">إضافة للسلة</button>
                            </div>
                        `;
                    }
                });
            } catch (error) {
                document.getElementById('loading').innerText = 'حدث خطأ في الاتصال بلوحة التحكم. تأكد من رابط النشر.';
            }
        }

        function addToCart(name, price) {
            cart.push({name, price});
            total += price;
            renderCart();
        }

        function renderCart() {
            const list = document.getElementById('cart-items');
            list.innerHTML = cart.map(item => `
                <div class="cart-item">
                    <span>${item.name}</span>
                    <span>${item.price}$</span>
                </div>
            `).join('');
            document.getElementById('total-val').innerText = total;
        }

        function sendOrder() {
            if(cart.length === 0) return alert("أضف خدمات أولاً");
            const method = document.getElementById('pay-method').value;
            let msg = `طلب جديد من متجر الشاهين:\n\n`;
            cart.forEach(i => msg += `- ${i.name} (${i.price}$)\n`);
            msg += `\nالمجموع: ${total}$\nطريقة الدفع: ${method}`;
            
            // توجيه الطلب إلى حسابك في تليغرام
            window.open(`https://t.me/wolfoldd?text=${encodeURIComponent(msg)}`, '_blank');
        }

        // تشغيل دالة تحميل المنتجات عند فتح الصفحة
        loadProducts();
    </script>
</body>
</html>
