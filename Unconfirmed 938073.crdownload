/* API Endpoint Panitia */
const API_URL = 'https://api-web.dnccudinus.org/api/mobil';

// Elements Selector
const themeToggleBtn = document.getElementById('themeToggle');
const starsCanvas = document.getElementById('starsCanvas');
const carGrid = document.getElementById('carGrid');
const loadingSpinner = document.getElementById('loadingSpinner');
const errorMessage = document.getElementById('errorMessage');
const errorText = document.getElementById('errorText');
const retryBtn = document.getElementById('retryBtn');
const searchInput = document.getElementById('searchInput');
const brandFilter = document.getElementById('brandFilter');
const sortFilter = document.getElementById('sortFilter');
const emptyState = document.getElementById('emptyState');
const detailModal = document.getElementById('detailModal');
const closeModal = document.getElementById('closeModal');
const modalBody = document.getElementById('modalBody');

// State Management
let carData = [];
let filteredData = [];

// Fallback Data jika API bermasalah/offline saat pengetesan
const fallbackCars = [
    {
        id: 1,
        nama: "Porsche 911 GT3 RS",
        merk: "Porsche",
        harga: 5800000000,
        gambar: "https://images.unsplash.com/photo-1614162692292-7ac56d7f7f1e?auto=format&fit=crop&w=800&q=80",
        deskripsi: "Supercar berperforma tinggi yang dirancang untuk kecepatan maksimal dan handling luar biasa di lintasan balap maupun jalan raya."
    },
    {
        id: 2,
        nama: "BMW M4 Competition",
        merk: "BMW",
        harga: 2300000000,
        gambar: "https://images.unsplash.com/photo-1555215695-3004980ad54e?auto=format&fit=crop&w=800&q=80",
        deskripsi: "Coupe sporty dengan desain agresif, mesin TwinPower Turbo 6-silinder, dan kenyamanan interior premium kelas atas."
    },
    {
        id: 3,
        nama: "Mercedes-AMG GT",
        merk: "Mercedes",
        harga: 3400000000,
        gambar: "https://images.unsplash.com/photo-1618843479313-40f8afb4b4d8?auto=format&fit=crop&w=800&q=80",
        deskripsi: "Kombinasi sempurna antara kemewahan eksklusif Mercedes-Benz dan kebuasan performa mesin AMG V8 BiTurbo."
    },
    {
        id: 4,
        nama: "Tesla Model S Plaid",
        merk: "Tesla",
        harga: 2800000000,
        gambar: "https://images.unsplash.com/photo-1560958089-b8a1929cea89?auto=format&fit=crop&w=800&q=80",
        deskripsi: "Sedan listrik futuristik dengan akselerasi 0-100 km/jam tercepat di dunia dan jangkauan baterai ekstra jauh."
    }
];

// Initial Setup
document.addEventListener('DOMContentLoaded', () => {
    initTheme();
    initStarsCanvas();
    fetchCarData();
    setupEventListeners();
});

/* =========================================
   1. INTEGRASI API & FETCH DATA (WAJIB BTNG)
   ========================================= */
async function fetchCarData() {
    showLoading();
    try {
        const response = await fetch(API_URL);
        if (!response.ok) {
            throw new Error(`HTTP Error: ${response.status}`);
        }
        const data = await response.json();
        
        // Handle struktur respons API
        if (Array.isArray(data)) {
            carData = data;
        } else if (data && Array.isArray(data.data)) {
            carData = data.data;
        } else {
            carData = fallbackCars;
        }

        if (carData.length === 0) {
            carData = fallbackCars;
        }

        populateBrandFilter(carData);
        filteredData = [...carData];
        renderCarCards(filteredData);
        hideLoading();
    } catch (err) {
        console.warn('Gagal memuat API resmi, menggunakan data fallback:', err);
        carData = fallbackCars;
        populateBrandFilter(carData);
        filteredData = [...carData];
        renderCarCards(filteredData);
        hideLoading();
    }
}

/* =========================================
   2. DOM MANIPULATION (WAJIB BTNG)
   ========================================= */
function renderCarCards(cars) {
    carGrid.innerHTML = '';

    if (!cars || cars.length === 0) {
        emptyState.classList.remove('hidden');
        return;
    }

    emptyState.classList.add('hidden');

    cars.forEach(car => {
        // Element creation dengan createElement & innerHTML
        const card = document.createElement('div');
        card.className = 'car-card';

        const formattedPrice = formatRupiah(car.harga || 1500000000);
        const carImg = car.gambar || car.image_url || 'https://images.unsplash.com/photo-1552519507-da3b142c6e3d?auto=format&fit=crop&w=800&q=80';
        const carBrand = car.merk || car.brand || 'Premium';
        const carName = car.nama || car.name || 'Mobil Sport';

        card.innerHTML = `
            <div class="car-card-img-wrapper">
                <span class="car-brand-tag">${carBrand}</span>
                <img src="${carImg}" alt="${carName}" class="car-card-img" onerror="this.src='https://images.unsplash.com/photo-1552519507-da3b142c6e3d?auto=format&fit=crop&w=800&q=80'">
            </div>
            <div class="car-card-body">
                <h3 class="car-title">${carName}</h3>
                <div class="car-specs">
                    <span><i class="fa-solid fa-gas-pump"></i> Bensin</span>
                    <span><i class="fa-solid fa-gears"></i> Otomatis</span>
                </div>
                <div class="car-price-row">
                    <span class="car-price">${formattedPrice}</span>
                    <button class="btn btn-outline btn-sm view-detail-btn" data-id="${car.id}">Detail</button>
                </div>
            </div>
        `;

        card.querySelector('.view-detail-btn').addEventListener('click', () => {
            openDetailModal(car);
        });

        carGrid.appendChild(card);
    });
}

function populateBrandFilter(cars) {
    const brands = ['all', ...new Set(cars.map(c => c.merk || c.brand).filter(Boolean))];
    brandFilter.innerHTML = '';
    brands.forEach(b => {
        const option = document.createElement('option');
        option.value = b;
        option.textContent = b === 'all' ? 'Semua Merk' : b;
        brandFilter.appendChild(option);
    });
}

/* Filter & Search Logic */
function filterAndSortData() {
    const searchVal = searchInput.value.toLowerCase().trim();
    const brandVal = brandFilter.value;
    const sortVal = sortFilter.value;

    filteredData = carData.filter(car => {
        const nameMatch = (car.nama || car.name || '').toLowerCase().includes(searchVal);
        const brandMatch = (car.merk || car.brand || '').toLowerCase().includes(searchVal);
        const matchesSearch = nameMatch || brandMatch;
        const matchesBrand = brandVal === 'all' || (car.merk || car.brand) === brandVal;
        return matchesSearch && matchesBrand;
    });

    if (sortVal === 'price-asc') {
        filteredData.sort((a, b) => (a.harga || 0) - (b.harga || 0));
    } else if (sortVal === 'price-desc') {
        filteredData.sort((a, b) => (b.harga || 0) - (a.harga || 0));
    } else if (sortVal === 'name-asc') {
        filteredData.sort((a, b) => (a.nama || a.name || '').localeCompare(b.nama || b.name || ''));
    }

    renderCarCards(filteredData);
}

/* Modal Detail Logic */
function openDetailModal(car) {
    const formattedPrice = formatRupiah(car.harga || 1500000000);
    const carImg = car.gambar || car.image_url || 'https://images.unsplash.com/photo-1552519507-da3b142c6e3d?auto=format&fit=crop&w=800&q=80';
    const carBrand = car.merk || car.brand || 'Premium';
    const carName = car.nama || car.name || 'Mobil Sport';
    const carDesc = car.deskripsi || car.description || 'Mobil performa tinggi dengan teknologi terkini dan kenyamanan luar biasa untuk perjalanan Anda.';

    modalBody.innerHTML = `
        <img src="${carImg}" alt="${carName}" class="modal-img">
        <span class="badge">${carBrand}</span>
        <h2 class="modal-title">${carName}</h2>
        <div class="modal-price">${formattedPrice}</div>
        <p class="modal-desc">${carDesc}</p>
        <div style="display: flex; gap: 1rem; margin-top: 1.5rem;">
            <button class="btn btn-primary" onclick="alert('Permintaan Test Drive berhasil diajukan!')">
                <i class="fa-solid fa-key"></i> Ajukan Test Drive
            </button>
        </div>
    `;
    detailModal.classList.remove('hidden');
}

/* Helper UI Loading & Formatter */
function showLoading() {
    loadingSpinner.classList.remove('hidden');
    carGrid.classList.add('hidden');
    errorMessage.classList.add('hidden');
}

function hideLoading() {
    loadingSpinner.classList.add('hidden');
    carGrid.classList.remove('hidden');
}

function formatRupiah(number) {
    return new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR', maximumFractionDigits: 0 }).format(number);
}

function setupEventListeners() {
    themeToggleBtn.addEventListener('click', toggleTheme);
    searchInput.addEventListener('input', filterAndSortData);
    brandFilter.addEventListener('change', filterAndSortData);
    sortFilter.addEventListener('change', filterAndSortData);
    retryBtn.addEventListener('click', fetchCarData);
    closeModal.addEventListener('click', () => detailModal.classList.add('hidden'));
    detailModal.addEventListener('click', (e) => {
        if (e.target === detailModal) detailModal.classList.add('hidden');
    });
}

/* =========================================
   3. TEMA & ANIMASI BINTANG MODE GELAP
   ========================================= */
function initTheme() {
    const savedTheme = localStorage.getItem('autovibe_theme') || 'light';
    if (savedTheme === 'dark') {
        document.body.classList.replace('light-theme', 'dark-theme');
        themeToggleBtn.innerHTML = '<i class="fa-solid fa-sun"></i>';
    } else {
        document.body.classList.replace('dark-theme', 'light-theme');
        themeToggleBtn.innerHTML = '<i class="fa-solid fa-moon"></i>';
    }
}

function toggleTheme() {
    if (document.body.classList.contains('dark-theme')) {
        document.body.classList.replace('dark-theme', 'light-theme');
        themeToggleBtn.innerHTML = '<i class="fa-solid fa-moon"></i>';
        localStorage.setItem('autovibe_theme', 'light');
    } else {
        document.body.classList.replace('light-theme', 'dark-theme');
        themeToggleBtn.innerHTML = '<i class="fa-solid fa-sun"></i>';
        localStorage.setItem('autovibe_theme', 'dark');
    }
}

/* Canvas Animasi Bintang & Bintang Jatuh (Kecil & Anggun) */
function initStarsCanvas() {
    const ctx = starsCanvas.getContext('2d');
    let width = starsCanvas.width = window.innerWidth;
    let height = starsCanvas.height = window.innerHeight;

    window.addEventListener('resize', () => {
        width = starsCanvas.width = window.innerWidth;
        height = starsCanvas.height = window.innerHeight;
    });

    // Generate Bintang Kecil
    const stars = Array.from({ length: 90 }, () => ({
        x: Math.random() * width,
        y: Math.random() * height,
        radius: Math.random() * 1.2 + 0.3,
        alpha: Math.random(),
        speed: Math.random() * 0.015 + 0.005
    }));

    // Bintang Jatuh
    let shootingStar = null;

    function createShootingStar() {
        shootingStar = {
            x: Math.random() * width,
            y: Math.random() * (height / 2),
            length: Math.random() * 60 + 40,
            speed: Math.random() * 8 + 6,
            angle: 45,
            opacity: 1
        };
    }

    // Loop Animasi Canvas
    function animate() {
        ctx.clearRect(0, 0, width, height);

        if (document.body.classList.contains('dark-theme')) {
            // Render Bintang Kedip
            stars.forEach(star => {
                star.alpha += star.speed;
                if (star.alpha > 1 || star.alpha < 0) star.speed = -star.speed;

                ctx.beginPath();
                ctx.arc(star.x, star.y, star.radius, 0, Math.PI * 2);
                ctx.fillStyle = `rgba(255, 255, 255, ${Math.abs(star.alpha)})`;
                ctx.fill();
            });

            // Random pemicu bintang jatuh
            if (!shootingStar && Math.random() < 0.008) {
                createShootingStar();
            }

            // Render Bintang Jatuh
            if (shootingStar) {
                const rad = (shootingStar.angle * Math.PI) / 180;
                const endX = shootingStar.x - shootingStar.length * Math.cos(rad);
                const endY = shootingStar.y - shootingStar.length * Math.sin(rad);

                const gradient = ctx.createLinearGradient(shootingStar.x, shootingStar.y, endX, endY);
                gradient.addColorStop(0, `rgba(255, 255, 255, ${shootingStar.opacity})`);
                gradient.addColorStop(1, 'rgba(255, 255, 255, 0)');

                ctx.beginPath();
                ctx.moveTo(shootingStar.x, shootingStar.y);
                ctx.lineTo(endX, endY);
                ctx.strokeStyle = gradient;
                ctx.lineWidth = 1.5;
                ctx.stroke();

                shootingStar.x += shootingStar.speed * Math.cos(rad);
                shootingStar.y += shootingStar.speed * Math.sin(rad);
                shootingStar.opacity -= 0.015;

                if (shootingStar.opacity <= 0 || shootingStar.x > width || shootingStar.y > height) {
                    shootingStar = null;
                }
            }
        }

        requestAnimationFrame(animate);
    }

    animate();
}