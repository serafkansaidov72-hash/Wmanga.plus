<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MangaShelf — Ваша библиотека манги</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: #0f172a;
            color: #e2e8f0;
            margin: 0;
            padding: 0;
        }
        
        .drop-zone {
            border: 3px dashed #6366f1;
            background: rgba(99, 102, 241, 0.1);
            transition: all 0.3s;
        }
        .drop-zone.drag-over {
            background: rgba(99, 102, 241, 0.2);
            border-color: #818cf8;
            transform: scale(1.02);
        }
        
        .manga-card {
            background: #1e293b;
            border-radius: 12px;
            overflow: hidden;
            transition: transform 0.3s, box-shadow 0.3s;
            cursor: pointer;
        }
        .manga-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 20px 40px rgba(99, 102, 241, 0.2);
        }
        
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.8);
            z-index: 1000;
            overflow-y: auto;
        }
        .modal.active {
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }
        
        .reader {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: #000;
            z-index: 2000;
            overflow-y: auto;
        }
        .reader.active {
            display: block;
        }
        
        .toast {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background: #10b981;
            color: white;
            padding: 12px 24px;
            border-radius: 8px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            transform: translateY(100px);
            opacity: 0;
            transition: all 0.3s;
            z-index: 3000;
        }
        .toast.show {
            transform: translateY(0);
            opacity: 1;
        }
        .toast.error {
            background: #ef4444;
        }
    </style>
</head>
<body>

    <!-- Header -->
    <nav style="background: rgba(15, 23, 42, 0.9); backdrop-filter: blur(10px); border-bottom: 1px solid #334155; position: fixed; top: 0; width: 100%; z-index: 100;">
        <div style="max-width: 1200px; margin: 0 auto; padding: 16px 24px; display: flex; justify-content: space-between; align-items: center;">
            <div style="display: flex; align-items: center; gap: 12px; cursor: pointer;" onclick="showLibrary()">
                <div style="width: 40px; height: 40px; background: linear-gradient(135deg, #6366f1, #a855f7); border-radius: 8px; display: flex; align-items: center; justify-content: center;">
                    <svg width="24" height="24" fill="white" viewBox="0 0 24 24"><path d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253"/></svg>
                </div>
                <span style="font-size: 20px; font-weight: bold; background: linear-gradient(90deg, #818cf8, #c084fc); -webkit-background-clip: text; -webkit-text-fill-color: transparent;">MangaShelf</span>
            </div>
            <button onclick="openModal()" style="background: #6366f1; color: white; border: none; padding: 10px 20px; border-radius: 8px; cursor: pointer; font-weight: 500; display: flex; align-items: center; gap: 8px;">
                <svg width="20" height="20" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M12 4v16m8-8H4"/></svg>
                Добавить мангу
            </button>
        </div>
    </nav>

    <!-- Main Content -->
    <main style="padding-top: 80px; max-width: 1200px; margin: 0 auto; padding-left: 24px; padding-right: 24px;">
        
        <!-- Stats -->
        <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 16px; margin-bottom: 24px; margin-top: 24px;">
            <div style="background: #1e293b; padding: 20px; border-radius: 12px; border: 1px solid #334155;">
                <div style="color: #94a3b8; font-size: 14px;">Всего тайтлов</div>
                <div style="font-size: 28px; font-weight: bold; color: white;" id="statTotal">0</div>
            </div>
            <div style="background: #1e293b; padding: 20px; border-radius: 12px; border: 1px solid #334155;">
                <div style="color: #94a3b8; font-size: 14px;">Недавно добавлено</div>
                <div style="font-size: 28px; font-weight: bold; color: #818cf8;" id="statRecent">0</div>
            </div>
        </div>

        <!-- Filters -->
        <div style="display: flex; gap: 8px; margin-bottom: 24px; flex-wrap: wrap;">
            <button onclick="setFilter('all')" class="filter-btn active" data-filter="all" style="padding: 8px 16px; border-radius: 8px; border: 1px solid #334155; background: #6366f1; color: white; cursor: pointer;">Все</button>
            <button onclick="setFilter('reading')" class="filter-btn" data-filter="reading" style="padding: 8px 16px; border-radius: 8px; border: 1px solid #334155; background: #1e293b; color: #cbd5e1; cursor: pointer;">Читаю</button>
            <button onclick="setFilter('completed')" class="filter-btn" data-filter="completed" style="padding: 8px 16px; border-radius: 8px; border: 1px solid #334155; background: #1e293b; color: #cbd5e1; cursor: pointer;">Прочитано</button>
            <button onclick="setFilter('planned')" class="filter-btn" data-filter="planned" style="padding: 8px 16px; border-radius: 8px; border: 1px solid #334155; background: #1e293b; color: #cbd5e1; cursor: pointer;">В планах</button>
        </div>

        <!-- Empty State -->
        <div id="emptyState" style="display: none; text-align: center; padding: 60px 20px;">
            <div style="width: 100px; height: 100px; background: #334155; border-radius: 50%; margin: 0 auto 20px; display: flex; align-items: center; justify-content: center;">
                <svg width="50" height="50" fill="#64748b" viewBox="0 0 24 24"><path d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253"/></svg>
            </div>
            <h3 style="color: white; margin-bottom: 8px;">Библиотека пуста</h3>
            <p style="color: #64748b; margin-bottom: 20px;">Добавьте свою первую мангу</p>
            <button onclick="openModal()" style="background: #6366f1; color: white; border: none; padding: 12px 24px; border-radius: 8px; cursor: pointer;">Загрузить мангу</button>
        </div>

        <!-- Grid -->
        <div id="mangaGrid" style="display: grid; grid-template-columns: repeat(auto-fill, minmax(160px, 1fr)); gap: 20px; padding-bottom: 40px;">
        </div>
    </main>

    <!-- Upload Modal -->
    <div id="uploadModal" class="modal">
        <div style="background: #1e293b; border: 1px solid #334155; border-radius: 16px; width: 100%; max-width: 600px; max-height: 90vh; overflow-y: auto;">
            <div style="padding: 24px; border-bottom: 1px solid #334155; display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; background: #1e293b; z-index: 10;">
                <h2 style="margin: 0; color: white; font-size: 20px;">Добавить мангу</h2>
                <button onclick="closeModal()" style="background: none; border: none; color: #94a3b8; cursor: pointer; font-size: 24px; line-height: 1;">×</button>
            </div>
            
            <div style="padding: 24px;">
                <!-- Title -->
                <div style="margin-bottom: 16px;">
                    <label style="display: block; color: #94a3b8; margin-bottom: 6px; font-size: 14px;">Название *</label>
                    <input type="text" id="inputTitle" style="width: 100%; padding: 12px; background: #0f172a; border: 1px solid #334155; border-radius: 8px; color: white; font-size: 16px; box-sizing: border-box;" placeholder="Введите название манги">
                </div>

                <!-- Status & Rating -->
                <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 16px; margin-bottom: 16px;">
                    <div>
                        <label style="display: block; color: #94a3b8; margin-bottom: 6px; font-size: 14px;">Статус</label>
                        <select id="inputStatus" style="width: 100%; padding: 12px; background: #0f172a; border: 1px solid #334155; border-radius: 8px; color: white; font-size: 16px;">
                            <option value="reading">Читаю</option>
                            <option value="completed">Прочитано</option>
                            <option value="planned">В планах</option>
                            <option value="dropped">Брошено</option>
                        </select>
                    </div>
                    <div>
                        <label style="display: block; color: #94a3b8; margin-bottom: 6px; font-size: 14px;">Рейтинг</label>
                        <select id="inputRating" style="width: 100%; padding: 12px; background: #0f172a; border: 1px solid #334155; border-radius: 8px; color: white; font-size: 16px;">
                            <option value="0">Не оценено</option>
                            <option value="10">10</option>
                            <option value="9">9</option>
                            <option value="8">8</option>
                            <option value="7">7</option>
                            <option value="6">6</option>
                        </select>
                    </div>
                </div>

                <!-- Cover -->
                <div style="margin-bottom: 24px;">
                    <label style="display: block; color: #94a3b8; margin-bottom: 6px; font-size: 14px;">Обложка</label>
                    <div style="display: flex; gap: 16px; align-items: flex-start;">
                        <div id="coverPreview" style="width: 100px; height: 150px; background: #334155; border-radius: 8px; display: flex; align-items: center; justify-content: center; overflow: hidden; border: 2px dashed #475569;">
                            <span style="color: #64748b; font-size: 12px; text-align: center;">Нет<br>обложки</span>
                        </div>
                        <div style="flex: 1;">
                            <input type="file" id="inputCover" accept="image/*" style="display: none;" onchange="handleCover(this)">
                            <button type="button" onclick="document.getElementById('inputCover').click()" style="background: #334155; color: white; border: none; padding: 10px 16px; border-radius: 8px; cursor: pointer; margin-bottom: 8px;">Выбрать обложку</button>
                            <p style="color: #64748b; font-size: 12px; margin: 0;">Если не выбрана, используется первая страница</p>
                        </div>
                    </div>
                </div>

                <!-- Files Drop Zone -->
                <div style="margin-bottom: 24px;">
                    <label style="display: block; color: #94a3b8; margin-bottom: 6px; font-size: 14px;">Страницы *</label>
                    <div id="dropZone" class="drop-zone" style="padding: 40px 20px; text-align: center; border-radius: 12px; cursor: pointer;">
                        <input type="file" id="inputFiles" multiple accept="image/*" style="display: none;" onchange="handleFiles(this.files)">
                        <svg width="48" height="48" fill="none" stroke="#6366f1" stroke-width="2" style="margin: 0 auto 12px;"><path d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"/></svg>
                        <p style="color: white; margin: 0 0 4px 0; font-weight: 500;">Нажмите или перетащите файлы</p>
                        <p style="color: #64748b; margin: 0; font-size: 14px;">Поддерживаются JPG, PNG, WebP</p>
                    </div>
                    
                    <!-- File List -->
                    <div id="fileList" style="margin-top: 12px; display: none;">
                        <p style="color: #94a3b8; font-size: 14px; margin-bottom: 8px;">Выбрано файлов: <span id="fileCount" style="color: white; font-weight: bold;">0</span></p>
                        <div id="fileListContainer" style="max-height: 150px; overflow-y: auto; background: #0f172a; border-radius: 8px; padding: 8px;"></div>
                    </div>
                </div>

                <!-- Submit -->
                <button id="btnSave" onclick="saveManga()" style="width: 100%; background: #6366f1; color: white; border: none; padding: 14px; border-radius: 8px; font-size: 16px; font-weight: 500; cursor: pointer; opacity: 0.5;" disabled>Сохранить мангу</button>
            </div>
        </div>
    </div>

    <!-- Reader -->
    <div id="reader" class="reader">
        <div style="position: fixed; top: 0; left: 0; right: 0; background: linear-gradient(to bottom, rgba(0,0,0,0.8), transparent); padding: 20px; z-index: 100; display: flex; justify-content: space-between; align-items: center;">
            <button onclick="closeReader()" style="background: rgba(255,255,255,0.1); border: none; color: white; width: 40px; height: 40px; border-radius: 8px; cursor: pointer; font-size: 20px;">×</button>
            <h3 id="readerTitle" style="color: white; margin: 0; font-size: 16px;"></h3>
            <div style="width: 40px;"></div>
        </div>
        
        <div style="padding: 80px 20px 100px; max-width: 800px; margin: 0 auto; text-align: center;">
            <div id="readerPages"></div>
        </div>

        <div style="position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); background: rgba(30, 41, 59, 0.9); padding: 12px 24px; border-radius: 24px; display: flex; gap: 16px; align-items: center; border: 1px solid #334155;">
            <button onclick="prevPage()" style="background: none; border: none; color: white; cursor: pointer; font-size: 18px;">←</button>
            <span id="pageIndicator" style="color: white; font-size: 14px; min-width: 60px; text-align: center;">1 / 1</span>
            <button onclick="nextPage()" style="background: none; border: none; color: white; cursor: pointer; font-size: 18px;">→</button>
        </div>
    </div>

    <!-- Toast -->
    <div id="toast" class="toast"></div>

    <script>
        // Data
        let library = JSON.parse(localStorage.getItem('mangaLib')) || [];
        let currentFilter = 'all';
        let currentManga = null;
        let currentPage = 0;
        
        // Upload state
        let selectedFiles = [];
        let coverData = null;

        // Init
        window.onload = function() {
            renderLibrary();
            setupDropZone();
            updateButton();
            
            // Title input listener
            document.getElementById('inputTitle').addEventListener('input', updateButton);
        };

        // Drop Zone Setup
        function setupDropZone() {
            const dz = document.getElementById('dropZone');
            const fileInput = document.getElementById('inputFiles');
            
            dz.onclick = function(e) {
                if (e.target !== fileInput) {
                    fileInput.click();
                }
            };
            
            ['dragenter', 'dragover', 'dragleave', 'drop'].forEach(eventName => {
                dz.addEventListener(eventName, preventDefaults, false);
            });
            
            function preventDefaults(e) {
                e.preventDefault();
                e.stopPropagation();
            }
            
            ['dragenter', 'dragover'].forEach(eventName => {
                dz.addEventListener(eventName, function() {
                    dz.classList.add('drag-over');
                }, false);
            });
            
            ['dragleave', 'drop'].forEach(eventName => {
                dz.addEventListener(eventName, function() {
                    dz.classList.remove('drag-over');
                }, false);
            });
            
            dz.addEventListener('drop', function(e) {
                const dt = e.dataTransfer;
                const files = dt.files;
                handleFiles(files);
            }, false);
        }

        function handleFiles(files) {
            console.log('Files selected:', files.length);
            const imageFiles = Array.from(files).filter(f => f.type.startsWith('image/'));
            
            if (imageFiles.length === 0) {
                showToast('Выберите изображения', 'error');
                return;
            }
            
            selectedFiles = [...selectedFiles, ...imageFiles];
            updateFileList();
            
            // Auto title
            const titleInput = document.getElementById('inputTitle');
            if (!titleInput.value && imageFiles.length > 0) {
                const name = imageFiles[0].name.replace(/\.[^/.]+$/, '').replace(/[_-]/g, ' ');
                titleInput.value = name;
            }
            
            // Auto cover
            if (!coverData && imageFiles.length > 0) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    coverData = e.target.result;
                    updateCoverPreview();
                };
                reader.readAsDataURL(imageFiles[0]);
            }
            
            updateButton();
            showToast(`Добавлено ${imageFiles.length} файл(ов)`);
        }

        function handleCover(input) {
            const file = input.files[0];
            if (!file) return;
            
            if (!file.type.startsWith('image/')) {
                showToast('Выберите изображение', 'error');
                return;
            }
            
            const reader = new FileReader();
            reader.onload = function(e) {
                coverData = e.target.result;
                updateCoverPreview();
                showToast('Обложка загружена');
            };
            reader.readAsDataURL(file);
        }

        function updateCoverPreview() {
            const preview = document.getElementById('coverPreview');
            if (coverData) {
                preview.innerHTML = `<img src="${coverData}" style="width: 100%; height: 100%; object-fit: cover;">`;
                preview.style.border = 'none';
            } else {
                preview.innerHTML = '<span style="color: #64748b; font-size: 12px; text-align: center;">Нет<br>обложки</span>';
                preview.style.border = '2px dashed #475569';
            }
        }

        function updateFileList() {
            const listDiv = document.getElementById('fileList');
            const container = document.getElementById('fileListContainer');
            const count = document.getElementById('fileCount');
            
            count.textContent = selectedFiles.length;
            
            if (selectedFiles.length === 0) {
                listDiv.style.display = 'none';
                return;
            }
            
            listDiv.style.display = 'block';
            container.innerHTML = selectedFiles.map((file, idx) => `
                <div style="dis