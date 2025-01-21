<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>我的日记本</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <!-- 顶部导航栏 -->
    <header>
        <nav>
            <div class="logo">我的日记本</div>
            <div class="nav-buttons">
                <button id="newDiary">写日记</button>
                <button id="exportBtn">导出</button>
                <button id="settings">设置</button>
                <button id="lockBtn" title="锁定日记">🔒</button>
            </div>
        </nav>
    </header>

    <!-- 主要内容区域 -->
    <main>
        <!-- 日记列表页面 -->
        <section id="diaryList" class="active">
            <div class="search-bar">
                <input type="search" placeholder="搜索日记...">
                <button class="search-btn">搜索</button>
            </div>
            
            <div class="diary-items">
                <!-- 日记项目示例 -->
                <article class="diary-item">
                    <div class="diary-date">2024-03-20</div>
                    <div class="diary-mood">😊 开心</div>
                    <h3 class="diary-title">今天真是个好天气</h3>
                    <p class="diary-preview">今天阳光明媚，和朋友一起...</p>
                    <div class="diary-tags">
                        <span class="tag">朋友</span>
                        <span class="tag">心情</span>
                    </div>
                </article>
            </div>
        </section>

        <!-- 写日记页面 -->
        <section id="diaryEditor" class="hidden">
            <div class="editor-toolbar">
                <button class="tool-btn"><i class="icon-image"></i></button>
                <button class="tool-btn"><i class="icon-voice"></i></button>
                <button class="tool-btn"><i class="icon-video"></i></button>
                <button class="tool-btn"><i class="icon-emoji"></i></button>
                <select class="font-family">
                    <option value="宋体">宋体</option>
                    <option value="楷体">楷体</option>
                    <option value="黑体">黑体</option>
                </select>
                <select class="font-size">
                    <option value="12">12px</option>
                    <option value="16">16px</option>
                    <option value="20">20px</option>
                    <option value="24">24px</option>
                    <option value="28">28px</option>
                </select>
            </div>
            
            <div class="editor-content">
                <input type="text" class="diary-title-input" placeholder="请输入标题...">
                <textarea class="diary-content-input" placeholder="开始写下今天的故事..."></textarea>
                
                <div class="diary-meta">
                    <div class="mood-selector">
                        <label>心情：</label>
                        <select>
                            <option value="happy">😊 开心</option>
                            <option value="sad">😢 难过</option>
                            <option value="calm">😌 平静</option>
                        </select>
                    </div>
                    <div class="tags-input">
                        <label>标签：</label>
                        <input type="text" placeholder="输入标签，按回车添加">
                        <div class="tags-container"></div>
                    </div>
                </div>
            </div>

            <div class="editor-footer">
                <button class="save-btn">保存</button>
                <button class="cancel-btn">取消</button>
            </div>
        </section>
    </main>

    <!-- 设置页面 -->
    <section id="settingsPanel" class="hidden">
        <h2>设置</h2>
        <div class="settings-group">
            <h3>主题设置</h3>
            <select id="themeSelector">
                <option value="simple">简洁主题</option>
                <option value="rich">丰富主题</option>
            </select>
        </div>
        
        <div class="settings-group">
            <h3>安全设置</h3>
            <button id="changePassword">修改密码</button>
        </div>
        
        <div class="settings-group">
            <h3>提醒设置</h3>
            <div class="reminder-settings">
                <input type="time" id="reminderTime">
                <select id="reminderFrequency">
                    <option value="daily">每天</option>
                    <option value="weekday">工作日</option>
                    <option value="weekly">每周</option>
                </select>
            </div>
        </div>

        <!-- 在设置面板中添加特效设置 -->
        <div class="settings-group">
            <h3>鼠标特效</h3>
            <div class="mouse-effect-settings">
                <div class="effect-switch">
                    <label class="switch">
                        <input type="checkbox" id="effectSwitch">
                        <span class="slider"></span>
                    </label>
                    <span>启用特效</span>
                </div>
                <select id="effectSelector" disabled>
                    <option value="redPacket">红包特效</option>
                    <option value="stars">星星特效</option>
                    <option value="hearts">爱心特效</option>
                    <option value="fireworks">烟花特效</option>
                    <option value="bubbles">泡泡特效</option>
                    <option value="cats">小猫特效</option>
                    <option value="dogs">小狗特效</option>
                    <option value="butterflies">蝴蝶特效</option>
                    <option value="leaves">落叶特效</option>
                    <option value="snowflakes">雪花特效</option>
                </select>
            </div>
        </div>
    </section>

    <script src="app.js"></script>
</body>
</html> 
// 全局状态管理
const state = {
    diaries: [],
    currentDiary: null,
    settings: {
        theme: 'simple',
        password: '12345',
        reminder: {
            time: '20:00',
            frequency: 'daily'
        },
        mouseEffect: {
            enabled: false,
            type: 'redPacket'
        }
    }
};

// 初始化应用
document.addEventListener('DOMContentLoaded', () => {
    initializeApp();
    loadDiaries();
    setupEventListeners();
    checkPassword();
    setupTagsInput();
    setupMouseEffects();
});

// 初始化应用
function initializeApp() {
    showLoading();
    
    // 从localStorage加载设置
    const savedSettings = localStorage.getItem('diary_settings');
    if (savedSettings) {
        try {
            state.settings = JSON.parse(savedSettings);
            // 确保mouseEffect设置存在
            if (!state.settings.mouseEffect) {
                state.settings.mouseEffect = {
                    enabled: false,
                    type: 'redPacket'
                };
            }
        } catch (e) {
            console.error('加载设置失败:', e);
            state.settings = {
                theme: 'simple',
                password: '12345',
                reminder: {
                    time: '20:00',
                    frequency: 'daily'
                },
                mouseEffect: {
                    enabled: false,
                    type: 'redPacket'
                }
            };
        }
    }
    
    // 确保设置被保存到 localStorage
    saveSettings();
    
    // 应用主题设置
    applyTheme(state.settings?.theme || 'simple');
    
    // 设置提醒
    setupReminders();
    
    setupMediaHandlers();
    
    // 添加导出按钮事件
    document.getElementById('exportBtn').addEventListener('click', exportDiaries);
    
    setupSearchFunction();
    
    setupEmojiPicker();
    
    // 初始化鼠标特效
    setupMouseEffects();
    
    setTimeout(hideLoading, 1000);
}

// 密码检查
function checkPassword() {
    if (state.settings.password && !sessionStorage.getItem('authenticated')) {
        showPasswordDialog();
    }
}

// 显示密码输入对话框
function showPasswordDialog() {
    const dialog = document.createElement('div');
    dialog.className = 'password-dialog';
    dialog.innerHTML = `
        <div class="password-content">
            <div class="lock-icon">🔒</div>
            <h3>欢迎回来</h3>
            <p class="subtitle">请输入密码解锁您的私密日记</p>
            <div class="password-input-container">
                <input type="password" id="passwordInput" maxlength="5" pattern="[0-9]*" placeholder="请输入5位数字密码">
                <button id="confirmPassword" class="confirm-btn disabled">确认</button>
            </div>
            <div class="error-message"></div>
        </div>
    `;
    document.body.appendChild(dialog);

    const input = dialog.querySelector('#passwordInput');
    const confirmBtn = dialog.querySelector('#confirmPassword');

    // 监听输入变化
    input.addEventListener('input', (e) => {
        if (e.target.value.length === 5) {
            confirmBtn.classList.remove('disabled');
        } else {
            confirmBtn.classList.add('disabled');
        }
    });

    // 添加回车键提交功能
    input.addEventListener('keypress', (e) => {
        if (e.key === 'Enter' && input.value.length === 5) {
            verifyPassword();
        }
    });

    confirmBtn.addEventListener('click', () => {
        if (input.value.length === 5) {
            verifyPassword();
        }
    });
    
    // 自动聚焦到输入框
    input.focus();

    // 添加模糊效果
    document.querySelector('main').classList.add('blur-content');
}

// 验证密码
let passwordAttempts = 0;
function verifyPassword() {
    const input = document.getElementById('passwordInput');
    const password = input.value;
    const errorMsg = document.querySelector('.error-message');
    const confirmBtn = document.querySelector('#confirmPassword');
    
    if (!password) {
        errorMsg.textContent = '请输入密码';
        return;
    }
    
    if (password === state.settings.password) {
        sessionStorage.setItem('authenticated', 'true');
        const dialog = document.querySelector('.password-dialog');
        if (dialog) {
            dialog.remove();
        }
        document.querySelector('main').classList.remove('blur-content');
        
        // 移除模糊效果并显示真实日记
        const diaryItems = document.querySelectorAll('.diary-item');
        diaryItems.forEach(item => {
            item.classList.remove('blur-item');
            item.style.animation = 'fadeIn 0.5s ease-out';
        });
        
        // 恢复之前的编辑状态
        const savedEditorState = sessionStorage.getItem('editorState');
        if (savedEditorState) {
            const editorState = JSON.parse(savedEditorState);
            const editor = document.getElementById('diaryEditor');
            
            if (editor.classList.contains('hidden')) {
                showEditor();
            }
            
            document.querySelector('.diary-title-input').value = editorState.title;
            document.querySelector('.diary-content-input').value = editorState.content;
            document.querySelector('.mood-selector select').value = editorState.mood;
            
            // 恢复标签
            const tagsContainer = document.querySelector('.tags-container');
            tagsContainer.innerHTML = '';
            editorState.tags.forEach(tag => {
                const tagElement = document.createElement('span');
                tagElement.className = 'tag';
                tagElement.textContent = tag;
                const deleteBtn = document.createElement('span');
                deleteBtn.className = 'delete-tag';
                deleteBtn.innerHTML = '×';
                deleteBtn.onclick = () => tagElement.remove();
                tagElement.appendChild(deleteBtn);
                tagsContainer.appendChild(tagElement);
            });
            
            // 清除保存的编辑状态
            sessionStorage.removeItem('editorState');
        }
        
        // 重新加载日记列表
        loadDiaries();
        
        passwordAttempts = 0;
    } else {
        passwordAttempts++;
        confirmBtn.classList.add('error');
        if (passwordAttempts >= 3) {
            errorMsg.textContent = '输入错误次数过多，请1分钟后重试';
            input.disabled = true;
            confirmBtn.disabled = true;
            setTimeout(() => {
                input.disabled = false;
                confirmBtn.disabled = false;
                confirmBtn.classList.remove('error');
                passwordAttempts = 0;
                errorMsg.textContent = '';
            }, 60000);
        } else {
            errorMsg.textContent = `密码错误，还剩${3 - passwordAttempts}次机会`;
            setTimeout(() => {
                confirmBtn.classList.remove('error');
            }, 1000);
        }
    }
}

// 设置事件监听器
function setupEventListeners() {
    // 新建日记按钮
    document.getElementById('newDiary').addEventListener('click', () => {
        showEditor();
    });
    
    // 设置按钮
    document.getElementById('settings').addEventListener('click', () => {
        showSettings();
    });
    
    // 保存按钮
    document.querySelector('.save-btn').addEventListener('click', saveDiary);
    
    // 取消按钮
    document.querySelector('.cancel-btn').addEventListener('click', () => {
        hideEditor();
    });
    
    // 主题选择
    document.getElementById('themeSelector')?.addEventListener('change', (e) => {
        applyTheme(e.target.value);
    });

    // 点击对话框外部关闭
    const statsDialog = document.getElementById('statisticsDialog');
    if (statsDialog) {
        statsDialog.addEventListener('click', (e) => {
            if (e.target === statsDialog) {
                closeStatisticsDialog();
            }
        });
    }

    // 设置面板的返回按钮
    const settingsBackBtn = document.createElement('button');
    settingsBackBtn.textContent = '返回';
    settingsBackBtn.className = 'back-btn';
    settingsBackBtn.onclick = hideSettings;
    document.getElementById('settingsPanel').insertBefore(
        settingsBackBtn,
        document.getElementById('settingsPanel').firstChild
    );

    // 修改密码按钮
    document.getElementById('changePassword').addEventListener('click', showChangePasswordDialog);

    // 提醒设置
    document.getElementById('reminderTime').addEventListener('change', (e) => {
        state.settings.reminder.time = e.target.value;
        saveSettings();
    });

    document.getElementById('reminderFrequency').addEventListener('change', (e) => {
        state.settings.reminder.frequency = e.target.value;
        saveSettings();
    });

    // 添加锁定按钮事件
    document.getElementById('lockBtn').addEventListener('click', () => {
        lockDiary();
    });
}

// 显示编辑器
function showEditor(diary = null) {
    const diaryList = document.getElementById('diaryList');
    const diaryEditor = document.getElementById('diaryEditor');
    
    diaryList.style.animation = 'fadeOut 0.3s ease-out forwards';
    
    setTimeout(() => {
        diaryList.classList.add('hidden');
        diaryEditor.classList.remove('hidden');
        diaryEditor.style.animation = 'fadeIn 0.3s ease-out';
        
        if (diary) {
            state.currentDiary = diary;
            fillEditorWithDiary(diary);
        } else {
            state.currentDiary = null;
            resetEditor();
        }
    }, 300);
}

// 填充编辑器内容
function fillEditorWithDiary(diary) {
    const titleInput = document.querySelector('.diary-title-input');
    const contentInput = document.querySelector('.diary-content-input');
    const moodSelect = document.querySelector('.mood-selector select');
    const tagsContainer = document.querySelector('.tags-container');

    // 填充标题和内容
    titleInput.value = diary.title || '';
    contentInput.value = diary.content || '';
    
    // 设置心情
    moodSelect.value = diary.mood || 'happy';
    
    // 清空并重新填充标签
    tagsContainer.innerHTML = '';
    if (diary.tags && diary.tags.length) {
        diary.tags.forEach(tagText => {
            const tag = document.createElement('span');
            tag.className = 'tag';
            tag.textContent = tagText;
            
            // 添加删除功能
            tag.addEventListener('click', () => {
                tagsContainer.removeChild(tag);
            });
            
            tagsContainer.appendChild(tag);
        });
    }
}

// 重置编辑器
function resetEditor() {
    const titleInput = document.querySelector('.diary-title-input');
    const contentInput = document.querySelector('.diary-content-input');
    const moodSelect = document.querySelector('.mood-selector select');
    const tagsContainer = document.querySelector('.tags-container');

    // 清空所有输入
    titleInput.value = '';
    contentInput.value = '';
    moodSelect.value = 'happy';
    tagsContainer.innerHTML = '';
}

// 隐藏编辑器
function hideEditor() {
    document.getElementById('diaryList').classList.remove('hidden');
    document.getElementById('diaryEditor').classList.add('hidden');
    state.currentDiary = null;
}

// 保存日记
function saveDiary() {
    const titleInput = document.querySelector('.diary-title-input');
    const contentInput = document.querySelector('.diary-content-input');
    const moodSelect = document.querySelector('.mood-selector select');
    
    // 验证必填字段
    if (!titleInput.value.trim()) {
        alert('请输入日记标题');
        return;
    }
    
    if (!contentInput.value.trim()) {
        alert('请输入日记内容');
        return;
    }

    const diary = {
        id: state.currentDiary?.id || Date.now(),
        title: titleInput.value.trim(),
        content: contentInput.value.trim(),
        mood: moodSelect.value,
        date: state.currentDiary?.date || new Date().toISOString(),
        tags: getSelectedTags()
    };
    
    if (state.currentDiary) {
        // 更新现有日记
        const index = state.diaries.findIndex(d => d.id === diary.id);
        if (index !== -1) {
            state.diaries[index] = diary;
        }
    } else {
        // 添加新日记
        state.diaries.unshift(diary);
    }
    
    saveDiariesToStorage();
    hideEditor();
    renderDiaryList();
}

// 从本地存储加载日记
function loadDiaries() {
    const container = document.querySelector('.diary-items');
    
    if (!sessionStorage.getItem('authenticated')) {
        // 显示模糊的占位日记
        container.innerHTML = Array(5).fill(0).map(() => `
            <article class="diary-item blur-item">
                <div class="diary-header">
                    <span class="diary-date">****-**-**</span>
                    <div class="diary-mood">🔒</div>
                </div>
                <h3 class="diary-title">
                    <span class="blur-text">************************</span>
                </h3>
                <p class="diary-preview">
                    <span class="blur-text">************************************************************************************************</span>
                </p>
                <div class="diary-tags">
                    <span class="tag blur-text">****</span>
                    <span class="tag blur-text">****</span>
                </div>
            </article>
        `).join('');
        return;
    }
    
    // 加载真实日记
    const savedDiaries = localStorage.getItem('diaries');
    if (savedDiaries) {
        state.diaries = JSON.parse(savedDiaries);
        renderDiaryList();
    }
}

// 保存日记到本地存储
function saveDiariesToStorage() {
    localStorage.setItem('diaries', JSON.stringify(state.diaries));
}

// 渲染日记列表
function renderDiaryList(diaries = state.diaries, searchKeyword = '') {
    const container = document.querySelector('.diary-items');
    
    if (diaries.length === 0) {
        container.innerHTML = `
            <div class="no-results">
                <div class="no-results-icon">🔍</div>
                <h3>未找到相关日记</h3>
                <p>换个关键词试试吧</p>
            </div>
        `;
        return;
    }

    container.innerHTML = diaries.map(diary => {
        let title = diary.title;
        let content = diary.content;
        let tags = diary.tags;

        // 如果有搜索关键词，添加高亮
        if (searchKeyword) {
            const highlight = text => text.replace(
                new RegExp(searchKeyword, 'gi'),
                match => `<span class="highlight">${match}</span>`
            );
            title = highlight(title);
            content = highlight(content);
            tags = tags.map(tag => highlight(tag));
        }

        return `
            <article class="diary-item" data-id="${diary.id}">
                <div class="diary-header">
                    <div class="diary-date">${formatDate(diary.date)}</div>
                    <button class="delete-btn" onclick="deleteDiary(${diary.id})">删除</button>
                </div>
                <div class="diary-mood">${getMoodEmoji(diary.mood)}</div>
                <h3 class="diary-title">${title}</h3>
                <p class="diary-preview">${content.substring(0, 100)}...</p>
                <div class="diary-tags">
                    ${tags.map(tag => `<span class="tag">${tag}</span>`).join('')}
                </div>
            </article>
        `;
    }).join('');

    // 添加点击事件
    container.querySelectorAll('.diary-item').forEach(item => {
        item.addEventListener('click', (e) => {
            if (!e.target.classList.contains('delete-btn')) {
                const diary = diaries.find(d => d.id === parseInt(item.dataset.id));
                showEditor(diary);
            }
        });
    });
}

// 设置提醒
function setupReminders() {
    if (!("Notification" in window)) {
        console.log("This browser does not support notifications");
        return;
    }
    
    // 请求通知权限
    Notification.requestPermission();
    
    // 设置定时检查
    setInterval(checkReminder, 60000); // 每分钟检查一次
}

// 检查是否需要提醒
function checkReminder() {
    const now = new Date();
    const [reminderHour, reminderMinute] = state.settings.reminder.time.split(':');
    
    if (now.getHours() === parseInt(reminderHour) && 
        now.getMinutes() === parseInt(reminderMinute)) {
        
        if (shouldRemindToday()) {
            showNotification();
        }
    }
}

// 判断今天是否需要提醒
function shouldRemindToday() {
    const today = new Date().getDay();
    const frequency = state.settings.reminder.frequency;
    
    switch (frequency) {
        case 'daily':
            return true;
        case 'weekday':
            return today >= 1 && today <= 5;
        case 'weekly':
            return today === 1; // 每周一
        default:
            return false;
    }
}

// 显示通知
function showNotification() {
    if (Notification.permission === "granted") {
        new Notification("写日记时间到啦！", {
            body: "记录今天的故事吧~",
            icon: "/icon.png"
        });
    }
}

// 工具函数
function formatDate(dateString) {
    return new Date(dateString).toLocaleDateString('zh-CN');
}

function getMoodEmoji(mood) {
    const moodEmojis = {
        happy: '😊',
        sad: '😢',
        calm: '😌'
    };
    return moodEmojis[mood] || '😊';
}

function getSelectedTags() {
    const tagsContainer = document.querySelector('.tags-container');
    return Array.from(tagsContainer.children).map(tag => tag.textContent);
}

function applyTheme(theme) {
    // 设置主题类名
    document.body.className = theme;
    
    // 更新状态
    if (state.settings) {
        state.settings.theme = theme;
    } else {
        state.settings = { theme };
    }

    // 根据主题调整界面样式
    const elements = {
        diaryItems: document.querySelectorAll('.diary-item'),
        editorElements: document.querySelectorAll('.editor-content, .editor-toolbar'),
        settingsPanel: document.getElementById('settingsPanel')
    };

    if (theme === 'rich') {
        // 为富主题添加样式
        elements.diaryItems.forEach(item => {
            item.style.backgroundColor = 'rgba(255, 255, 255, 0.9)';
        });
        elements.editorElements.forEach(el => {
            el.style.backgroundColor = 'rgba(255, 255, 255, 0.9)';
        });
        if (elements.settingsPanel) {
            elements.settingsPanel.style.backgroundColor = 'rgba(255, 255, 255, 0.9)';
        }
    } else {
        // 重置为简洁主题的样式
        elements.diaryItems.forEach(item => {
            item.style.backgroundColor = '#fff';
        });
        elements.editorElements.forEach(el => {
            el.style.backgroundColor = '#fff';
        });
        if (elements.settingsPanel) {
            elements.settingsPanel.style.backgroundColor = '#fff';
        }
    }

    // 保存设置
    saveSettings();
}

function saveSettings() {
    try {
        localStorage.setItem('diary_settings', JSON.stringify(state.settings));
    } catch (e) {
        console.error('保存设置失败:', e);
    }
}

// 多媒体处理相关函数
function setupMediaHandlers() {
    // 图片上传
    document.querySelector('.icon-image').parentElement.addEventListener('click', () => {
        const input = document.createElement('input');
        input.type = 'file';
        input.accept = 'image/*';
        input.onchange = handleImageUpload;
        input.click();
    });

    // 语音录制
    document.querySelector('.icon-voice').parentElement.addEventListener('click', () => {
        if (state.isRecording) {
            stopRecording();
        } else {
            startRecording();
        }
    });

    // 视频上传
    document.querySelector('.icon-video').parentElement.addEventListener('click', () => {
        const input = document.createElement('input');
        input.type = 'file';
        input.accept = 'video/*';
        input.onchange = handleVideoUpload;
        input.click();
    });
}

// 图片处理
async function handleImageUpload(event) {
    const file = event.target.files[0];
    if (!file) return;

    try {
        const base64 = await convertFileToBase64(file);
        insertMediaToEditor('image', base64);
    } catch (error) {
        console.error('图片上传失败:', error);
        alert('图片上传失败，请重试');
    }
}

// 语音录制
let mediaRecorder = null;
let audioChunks = [];

async function startRecording() {
    try {
        const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
        mediaRecorder = new MediaRecorder(stream);
        
        mediaRecorder.ondataavailable = (event) => {
            audioChunks.push(event.data);
        };

        mediaRecorder.onstop = async () => {
            const audioBlob = new Blob(audioChunks, { type: 'audio/wav' });
            const base64 = await convertFileToBase64(audioBlob);
            insertMediaToEditor('audio', base64);
            audioChunks = [];
        };

        mediaRecorder.start();
        state.isRecording = true;
        updateRecordingUI(true);
        
        // 5分钟后自动停止
        setTimeout(() => {
            if (mediaRecorder.state === 'recording') {
                stopRecording();
            }
        }, 300000);
    } catch (error) {
        console.error('录音失败:', error);
        alert('无法访问麦克风，请检查权限设置');
    }
}

function stopRecording() {
    if (mediaRecorder && mediaRecorder.state === 'recording') {
        mediaRecorder.stop();
        mediaRecorder.stream.getTracks().forEach(track => track.stop());
        state.isRecording = false;
        updateRecordingUI(false);
    }
}

// 视频处理
async function handleVideoUpload(event) {
    const file = event.target.files[0];
    if (!file) return;

    if (file.size > 100 * 1024 * 1024) { // 100MB限制
        alert('视频大小不能超过100MB');
        return;
    }

    try {
        const base64 = await convertFileToBase64(file);
        insertMediaToEditor('video', base64);
    } catch (error) {
        console.error('视频上传失败:', error);
        alert('视频上传失败，请重试');
    }
}

// 工具函数
function convertFileToBase64(file) {
    return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.onload = () => resolve(reader.result);
        reader.onerror = reject;
        reader.readAsDataURL(file);
    });
}

function insertMediaToEditor(type, data) {
    const editor = document.querySelector('.diary-content-input');
    let mediaTag = '';
    
    switch (type) {
        case 'image':
            mediaTag = `\n[图片]\n<img src="${data}" style="max-width:100%">\n`;
            break;
        case 'audio':
            mediaTag = `\n[语音]\n<audio controls src="${data}"></audio>\n`;
            break;
        case 'video':
            mediaTag = `\n[视频]\n<video controls src="${data}" style="max-width:100%"></video>\n`;
            break;
    }
    
    const cursorPos = editor.selectionStart;
    const content = editor.value;
    editor.value = content.substring(0, cursorPos) + mediaTag + content.substring(cursorPos);
}

function updateRecordingUI(isRecording) {
    const recordBtn = document.querySelector('.icon-voice').parentElement;
    recordBtn.style.backgroundColor = isRecording ? '#ff4444' : '';
    recordBtn.title = isRecording ? '点击停止录音' : '点击开始录音';
}

// 数据导出功能
function exportDiaries() {
    const diariesText = state.diaries.map(diary => {
        return `
日期：${formatDate(diary.date)}
心情：${getMoodEmoji(diary.mood)}
标题：${diary.title}
标签：${diary.tags.join(', ')}
内容：
${diary.content}
-------------------
`;
    }).join('\n');

    const blob = new Blob([diariesText], { type: 'text/plain;charset=utf-8' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = `我的日记_${formatDate(new Date())}.txt`;
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
    URL.revokeObjectURL(url);
}

// 添加删除日记的函数
function deleteDiary(id) {
    if (confirm('确定要删除这篇日记吗？')) {
        state.diaries = state.diaries.filter(diary => diary.id !== id);
        saveDiariesToStorage();
        renderDiaryList();
    }
}

// 添加标签输入功能
function setupTagsInput() {
    const tagInput = document.querySelector('.tags-input input');
    const tagsContainer = document.querySelector('.tags-container');

    tagInput.addEventListener('keydown', (e) => {
        if (e.key === 'Enter' && tagInput.value.trim()) {
            e.preventDefault();
            const tag = document.createElement('span');
            tag.className = 'tag';
            tag.textContent = tagInput.value.trim();
            
            // 添加删除功能
            tag.addEventListener('click', () => {
                tagsContainer.removeChild(tag);
            });

            tagsContainer.appendChild(tag);
            tagInput.value = '';
        }
    });
}

// 修改 closeStatisticsDialog 函数
function closeStatisticsDialog() {
    const dialog = document.getElementById('statisticsDialog');
    dialog.classList.add('hidden');
}

// 显示设置面板
function showSettings() {
    document.getElementById('diaryList').classList.add('hidden');
    document.getElementById('diaryEditor').classList.add('hidden');
    document.getElementById('settingsPanel').classList.remove('hidden');

    // 填充当前设置
    document.getElementById('themeSelector').value = state.settings.theme;
    document.getElementById('reminderTime').value = state.settings.reminder.time;
    document.getElementById('reminderFrequency').value = state.settings.reminder.frequency;
}

// 隐藏设置面板
function hideSettings() {
    document.getElementById('settingsPanel').classList.add('hidden');
    document.getElementById('diaryList').classList.remove('hidden');
}

// 修改密码
function showChangePasswordDialog() {
    const dialog = document.createElement('div');
    dialog.className = 'password-dialog';
    dialog.innerHTML = `
        <div class="password-content">
            <h3>修改密码</h3>
            ${state.settings.password ? `
                <input type="password" id="oldPassword" placeholder="请输入原密码" maxlength="5" pattern="[0-9]*">
            ` : ''}
            <input type="password" id="newPassword" placeholder="请输入新密码(5位数字)" maxlength="5" pattern="[0-9]*">
            <input type="password" id="confirmPassword" placeholder="请确认新密码" maxlength="5" pattern="[0-9]*">
            <div class="error-message"></div>
            <div class="dialog-buttons">
                <button onclick="changePassword()">确认</button>
                <button onclick="closePasswordDialog()">取消</button>
            </div>
        </div>
    `;
    document.body.appendChild(dialog);
}

// 关闭密码对话框
function closePasswordDialog() {
    const dialog = document.querySelector('.password-dialog');
    if (dialog) {
        dialog.remove();
    }
}

// 修改密码
function changePassword() {
    const oldPassword = document.getElementById('oldPassword')?.value;
    const newPassword = document.getElementById('newPassword').value;
    const confirmPassword = document.getElementById('confirmPassword').value;
    const errorMsg = document.querySelector('.password-dialog .error-message');

    // 验证原密码
    if (state.settings.password && oldPassword !== state.settings.password) {
        errorMsg.textContent = '原密码错误';
        return;
    }

    // 验证新密码
    if (!/^\d{5}$/.test(newPassword)) {
        errorMsg.textContent = '新密码必须是5位数字';
        return;
    }

    if (newPassword !== confirmPassword) {
        errorMsg.textContent = '两次输入的密码不一致';
        return;
    }

    // 更新密码
    state.settings.password = newPassword;
    saveSettings();
    closePasswordDialog();
    alert('密码修改成功');
}

// 添加加载动画
function showLoading() {
    const loading = document.createElement('div');
    loading.className = 'loading';
    document.body.appendChild(loading);
}

function hideLoading() {
    const loading = document.querySelector('.loading');
    if (loading) {
        loading.remove();
    }
}

// 添加平滑滚动
document.querySelectorAll('a[href^="#"]').forEach(anchor => {
    anchor.addEventListener('click', function (e) {
        e.preventDefault();
        document.querySelector(this.getAttribute('href')).scrollIntoView({
            behavior: 'smooth'
        });
    });
});

// 为按钮添加涟漪效果
function createRipple(event) {
    const button = event.currentTarget;
    const ripple = document.createElement('span');
    const rect = button.getBoundingClientRect();
    
    ripple.style.position = 'absolute';
    ripple.style.top = `${event.clientY - rect.top}px`;
    ripple.style.left = `${event.clientX - rect.left}px`;
    ripple.style.width = ripple.style.height = '0';
    ripple.style.transform = 'translate(-50%, -50%)';
    ripple.style.borderRadius = '50%';
    ripple.style.background = 'rgba(255, 255, 255, 0.5)';
    ripple.style.transition = 'all 0.5s ease-out';
    
    button.appendChild(ripple);
    
    requestAnimationFrame(() => {
        ripple.style.width = ripple.style.height = `${Math.max(rect.width, rect.height) * 2}px`;
        ripple.style.opacity = '0';
    });
    
    setTimeout(() => ripple.remove(), 500);
}

// 为所有按钮添加涟漪效果
document.querySelectorAll('button').forEach(button => {
    button.addEventListener('click', createRipple);
});

// 添加搜索功能
function setupSearchFunction() {
    const searchInput = document.querySelector('.search-bar input');
    const searchBtn = document.querySelector('.search-btn');

    // 搜索按钮点击事件
    searchBtn.addEventListener('click', () => {
        performSearch(searchInput.value);
    });

    // 输入框回车事件
    searchInput.addEventListener('keypress', (e) => {
        if (e.key === 'Enter') {
            performSearch(searchInput.value);
        }
    });

    // 添加清除搜索按钮
    const clearSearchBtn = document.createElement('button');
    clearSearchBtn.className = 'clear-search-btn hidden';
    clearSearchBtn.innerHTML = '✕';
    searchInput.parentNode.appendChild(clearSearchBtn);

    clearSearchBtn.addEventListener('click', () => {
        searchInput.value = '';
        clearSearchBtn.classList.add('hidden');
        renderDiaryList(state.diaries); // 显示所有日记
    });

    // 监听输入变化
    searchInput.addEventListener('input', () => {
        clearSearchBtn.classList.toggle('hidden', !searchInput.value);
    });
}

// 执行搜索
function performSearch(keyword) {
    if (!keyword.trim()) {
        renderDiaryList(state.diaries);
        return;
    }

    const searchResults = state.diaries.filter(diary => {
        const searchText = keyword.toLowerCase();
        return (
            diary.title.toLowerCase().includes(searchText) ||
            diary.content.toLowerCase().includes(searchText) ||
            diary.tags.some(tag => tag.toLowerCase().includes(searchText))
        );
    });

    // 渲染搜索结果
    renderDiaryList(searchResults, keyword);
}

// 添加表情和GIF相关功能
function setupEmojiPicker() {
    const emojiBtn = document.querySelector('.icon-emoji').parentElement;
    
    // 创建表情选择器面板
    const emojiPicker = document.createElement('div');
    emojiPicker.className = 'emoji-picker hidden';
    emojiPicker.innerHTML = `
        <div class="emoji-tabs">
            <button class="active" data-tab="emoji">表情</button>
            <button data-tab="gif">GIF</button>
            <button data-tab="custom">自定义</button>
        </div>
        <div class="emoji-content">
            <div class="tab-panel active" id="emoji-panel">
                <div class="emoji-categories">
                    <button data-category="recent">最近使用</button>
                    <button data-category="smileys">笑脸</button>
                    <button data-category="animals">动物</button>
                    <button data-category="food">食物</button>
                    <button data-category="activities">活动</button>
                    <button data-category="objects">物品</button>
                </div>
                <div class="emoji-grid"></div>
            </div>
            <div class="tab-panel" id="gif-panel">
                <div class="gif-search">
                    <input type="text" placeholder="搜索GIF...">
                    <button class="gif-search-btn">搜索</button>
                </div>
                <div class="gif-grid"></div>
            </div>
            <div class="tab-panel" id="custom-panel">
                <div class="custom-emoji-upload">
                    <label class="upload-btn">
                        <input type="file" accept="image/gif,image/png,image/jpeg" multiple>
                        上传表情包
                    </label>
                </div>
                <div class="custom-emoji-grid"></div>
            </div>
        </div>
    `;
    
    document.querySelector('.editor-toolbar').appendChild(emojiPicker);
    
    // 加载默认表情
    const defaultEmojis = {
        smileys: ['😀', '😃', '😄', '😁', '😅', '😂', '🤣', '😊', '😇', '🙂', '🙃', '😉', '😌', '😍', '🥰', '😘'],
        animals: ['🐶', '🐱', '🐭', '🐹', '🐰', '🦊', '🐻', '🐼', '🐨', '🐯', '🦁', '🐮', '🐷', '🐸'],
        food: ['🍎', '🍐', '🍊', '🍋', '🍌', '🍉', '🍇', '🍓', '🍈', '🍒', '🍑', '🥭', '🍍'],
        activities: ['⚽', '🏀', '🏈', '⚾', '🥎', '🎾', '🏐', '🏉', '🎱', '🏓', '🏸', '🏒', '🏑'],
        objects: ['📱', '💻', '⌚', '📷', '📸', '📹', '🎥', '📽', '🎞', '📞', '☎️', '📟', '📠']
    };

    // 初始化表情网格
    function initEmojiGrid(category) {
        const grid = emojiPicker.querySelector('.emoji-grid');
        grid.innerHTML = defaultEmojis[category].map(emoji => 
            `<button class="emoji-item" data-emoji="${emoji}">${emoji}</button>`
        ).join('');
    }

    // 加载自定义表情
    function loadCustomEmojis() {
        const customEmojis = JSON.parse(localStorage.getItem('customEmojis') || '[]');
        const grid = emojiPicker.querySelector('.custom-emoji-grid');
        grid.innerHTML = customEmojis.map(emoji => `
            <div class="custom-emoji-item">
                <img src="${emoji.url}" alt="${emoji.name}">
                <button class="delete-emoji" data-id="${emoji.id}">×</button>
            </div>
        `).join('');
    }

    // 处理表情点击
    emojiPicker.addEventListener('click', (e) => {
        if (e.target.classList.contains('emoji-item')) {
            insertEmoji(e.target.dataset.emoji);
        } else if (e.target.closest('.custom-emoji-item')) {
            const img = e.target.closest('.custom-emoji-item').querySelector('img');
            insertCustomEmoji(img.src);
        }
    });

    // 处理标签切换
    emojiPicker.querySelector('.emoji-tabs').addEventListener('click', (e) => {
        if (e.target.dataset.tab) {
            const tab = e.target.dataset.tab;
            emojiPicker.querySelectorAll('.emoji-tabs button').forEach(btn => 
                btn.classList.toggle('active', btn === e.target)
            );
            emojiPicker.querySelectorAll('.tab-panel').forEach(panel => 
                panel.classList.toggle('active', panel.id === `${tab}-panel`)
            );
        }
    });

    // 处理表情分类切换
    emojiPicker.querySelector('.emoji-categories').addEventListener('click', (e) => {
        if (e.target.dataset.category) {
            initEmojiGrid(e.target.dataset.category);
        }
    });

    // 处理自定义表情上传
    const fileInput = emojiPicker.querySelector('input[type="file"]');
    fileInput.addEventListener('change', async (e) => {
        const files = Array.from(e.target.files);
        for (const file of files) {
            if (file.size > 2 * 1024 * 1024) { // 2MB限制
                alert('文件大小不能超过2MB');
                continue;
            }
            
            try {
                const base64 = await convertFileToBase64(file);
                const customEmoji = {
                    id: Date.now(),
                    name: file.name,
                    url: base64
                };
                
                const customEmojis = JSON.parse(localStorage.getItem('customEmojis') || '[]');
                customEmojis.push(customEmoji);
                localStorage.setItem('customEmojis', JSON.stringify(customEmojis));
                loadCustomEmojis();
            } catch (error) {
                console.error('表情上传失败:', error);
                alert('表情上传失败，请重试');
            }
        }
        fileInput.value = ''; // 重置输入
    });

    // 显示/隐藏表情选择器
    emojiBtn.addEventListener('click', () => {
        emojiPicker.classList.toggle('hidden');
        if (!emojiPicker.classList.contains('hidden')) {
            initEmojiGrid('smileys');
            loadCustomEmojis();
        }
    });

    // 点击外部关闭表情选择器
    document.addEventListener('click', (e) => {
        if (!emojiPicker.contains(e.target) && !emojiBtn.contains(e.target)) {
            emojiPicker.classList.add('hidden');
        }
    });
}

// 插入表情到编辑器
function insertEmoji(emoji) {
    const editor = document.querySelector('.diary-content-input');
    const start = editor.selectionStart;
    const end = editor.selectionEnd;
    const text = editor.value;
    editor.value = text.substring(0, start) + emoji + text.substring(end);
    editor.selectionStart = editor.selectionEnd = start + emoji.length;
    editor.focus();
}

// 插入自定义表情到编辑器
function insertCustomEmoji(url) {
    const editor = document.querySelector('.diary-content-input');
    const imgTag = `\n<img src="${url}" class="custom-emoji" style="max-width:100px;">\n`;
    const start = editor.selectionStart;
    const end = editor.selectionEnd;
    const text = editor.value;
    editor.value = text.substring(0, start) + imgTag + text.substring(end);
    editor.selectionStart = editor.selectionEnd = start + imgTag.length;
    editor.focus();
}

// 添加鼠标特效管理
function setupMouseEffects() {
    const effectSwitch = document.getElementById('effectSwitch');
    const effectSelector = document.getElementById('effectSelector');
    let currentEffect = null;

    // 从设置中加载特效配置
    effectSwitch.checked = state.settings.mouseEffect.enabled;
    effectSelector.value = state.settings.mouseEffect.type;
    effectSelector.disabled = !state.settings.mouseEffect.enabled;

    // 根据初始状态设置特效
    if (state.settings.mouseEffect.enabled) {
        currentEffect = new MouseEffect(state.settings.mouseEffect.type);
    }

    // 监听开关变化
    effectSwitch.addEventListener('change', (e) => {
        effectSelector.disabled = !e.target.checked;
        state.settings.mouseEffect.enabled = e.target.checked;
        
        if (e.target.checked) {
            currentEffect = new MouseEffect(effectSelector.value);
        } else {
            if (currentEffect) {
                currentEffect.destroy();
                currentEffect = null;
            }
        }
        
        saveSettings();
    });

    // 监听特效选择变化
    effectSelector.addEventListener('change', (e) => {
        if (effectSwitch.checked) {
            if (currentEffect) {
                currentEffect.destroy();
            }
            currentEffect = new MouseEffect(e.target.value);
            state.settings.mouseEffect.type = e.target.value;
            saveSettings();
        }
    });
}

// 鼠标特效类
class MouseEffect {
    constructor(type) {
        this.type = type;
        this.particles = [];
        this.maxParticles = 30;
        this.lastTime = 0;
        this.throttleDelay = 50;
        this.boundMouseMove = this.handleMouseMove.bind(this);
        
        // 清除所有现有的特效元素
        document.querySelectorAll('.mouse-effect').forEach(el => el.remove());
        
        // 添加事件监听
        document.addEventListener('mousemove', this.boundMouseMove);
    }

    handleMouseMove(e) {
        const currentTime = Date.now();
        if (currentTime - this.lastTime > this.throttleDelay) {
            this.createParticle(e.clientX, e.clientY);
            this.lastTime = currentTime;
        }
    }

    createParticle(x, y) {
        const particle = document.createElement('div');
        particle.className = `mouse-effect ${this.type}`;
        particle.style.left = x + 'px';
        particle.style.top = y + 'px';
        
        // 根据特效类型设置样式和图片
        switch(this.type) {
            case 'redPacket':
                particle.style.backgroundImage = "url('data:image/svg+xml,<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 24 32\"><path fill=\"%23e74c3c\" d=\"M2,0 h20 a2,2 0 0 1 2,2 v28 a2,2 0 0 1 -2,2 h-20 a2,2 0 0 1 -2,-2 v-28 a2,2 0 0 1 2,-2 z\"/><path fill=\"%23c0392b\" d=\"M1,4 h22 v2 h-22 z\"/><path fill=\"%23f1c40f\" d=\"M8,2 h8 v4 h-8 z\"/></svg>')";
                particle.style.width = '30px';
                particle.style.height = '40px';
                break;
            case 'stars':
                particle.style.backgroundImage = "url('data:image/svg+xml,<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 24 24\"><path fill=\"%23FFD700\" d=\"M12 0l3.09 9.26L24 12l-8.91 2.74L12 24l-3.09-9.26L0 12l8.91-2.74z\"/></svg>')";
                particle.style.width = '20px';
                particle.style.height = '20px';
                break;
            case 'hearts':
                particle.style.backgroundImage = "url('data:image/svg+xml,<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 24 24\"><path fill=\"%23FF69B4\" d=\"M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z\"/></svg>')";
                particle.style.width = '20px';
                particle.style.height = '20px';
                break;
            case 'fireworks':
                particle.style.background = 'radial-gradient(circle, #ff0000, #ffff00, #00ff00, #00ffff, #0000ff, #ff00ff)';
                particle.style.width = '10px';
                particle.style.height = '10px';
                particle.style.borderRadius = '50%';
                break;
            case 'bubbles':
                particle.style.background = 'radial-gradient(circle, rgba(255,255,255,0.8), rgba(173,216,230,0.4))';
                particle.style.width = '15px';
                particle.style.height = '15px';
                particle.style.borderRadius = '50%';
                break;
            case 'cats':
                particle.style.backgroundImage = "url('data:image/svg+xml,<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 24 24\"><path fill=\"%23000\" d=\"M12,8L10.67,8.09C9.81,7.07 7.4,4.5 5,4.5C5,4.5 3.03,7.46 4.96,11.41C4.41,12.24 4.07,12.67 4,13.66L2.07,13.95L2.28,14.93L4.04,14.67L4.18,15.38L2.61,16.32L3.08,17.21L4.53,16.32C5.68,18.76 8.59,20 12,20C15.41,20 18.32,18.76 19.47,16.32L20.92,17.21L21.39,16.32L19.82,15.38L19.96,14.67L21.72,14.93L21.93,13.95L20,13.66C19.93,12.67 19.59,12.24 19.04,11.41C20.97,7.46 19,4.5 19,4.5C16.6,4.5 14.19,7.07 13.33,8.09L12,8Z\"/></svg>')";
                particle.style.width = '25px';
                particle.style.height = '25px';
                break;
            case 'dogs':
                particle.style.backgroundImage = "url('data:image/svg+xml,<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 24 24\"><path fill=\"%23000\" d=\"M18,4C16.29,4 15,5.29 15,7A3,3 0 0,0 18,10A3,3 0 0,0 21,7C21,5.29 19.71,4 18,4M6,4C4.29,4 3,5.29 3,7A3,3 0 0,0 6,10A3,3 0 0,0 9,7C9,5.29 7.71,4 6,4M18,13C14.69,13 12,14.79 12,17V20H24V17C24,14.79 21.31,13 18,13M6,13C2.69,13 0,14.79 0,17V20H12V17C12,14.79 9.31,13 6,13Z\"/></svg>')";
                particle.style.width = '25px';
                particle.style.height = '25px';
                break;
            case 'butterflies':
                particle.style.backgroundImage = "url('data:image/svg+xml,<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 24 24\"><path fill=\"%23FF69B4\" d=\"M12.27 2.85c2.35-.7 4.44.53 5.55 2.51.4.72.6 1.46.55 2.15.46-.62 1.14-1.15 2.05-1.46z\"/></svg>')";
                particle.style.width = '20px';
                particle.style.height = '20px';
                break;
            case 'leaves':
                particle.style.backgroundImage = "url('data:image/svg+xml,<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 24 24\"><path fill=\"%234CAF50\" d=\"M17,8C8,10 5.9,16.17 3.82,21.34L5.71,22L6.66,19.7C7.14,19.87 7.64,20 8,20C19,20 22,3 22,3C21,5 14,5.25 9,6.25C4,7.25 2,11.5 2,13.5C2,15.5 3.75,17.25 3.75,17.25C7,8 17,8 17,8Z\"/></svg>')";
                particle.style.width = '20px';
                particle.style.height = '20px';
                break;
            case 'snowflakes':
                particle.style.backgroundImage = "url('data:image/svg+xml,<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 24 24\"><path fill=\"%2387CEEB\" d=\"M20.79,13.95L18.46,14.57L16.46,13.44V10.56L18.46,9.43L20.79,10.05L21.31,8.12L19.54,7.65L20,5.88L18.07,5.36L17.45,7.69L15.45,8.82L13,7.38V5.12L14.71,3.41L13.29,2L12,3.29L10.71,2L9.29,3.41L11,5.12V7.38L8.5,8.82L6.5,7.69L5.92,5.36L4,5.88L4.47,7.65L2.7,8.12L3.22,10.05L5.55,9.43L7.55,10.56V13.45L5.55,14.58L3.22,13.96L2.7,15.89L4.47,16.36L4,18.12L5.93,18.64L6.55,16.31L8.55,15.18L11,16.62V18.88L9.29,20.59L10.71,22L12,20.71L13.29,22L14.7,20.59L13,18.88V16.62L15.5,15.17L17.5,16.3L18.12,18.63L20,18.12L19.53,16.35L21.3,15.88L20.79,13.95M9.5,10.56L12,9.11L14.5,10.56V13.44L12,14.89L9.5,13.44V10.56Z\"/></svg>')";
                particle.style.width = '20px';
                particle.style.height = '20px';
                break;
        }

        document.body.appendChild(particle);
        this.applyEffectAnimation(particle);
        
        this.particles.push(particle);
        if (this.particles.length > this.maxParticles) {
            const oldestParticle = this.particles.shift();
            oldestParticle.remove();
        }
    }

    applyEffectAnimation(particle) {
        const animations = {
            redPacket: {
                keyframes: [
                    { transform: 'translate(0, 0) rotate(0deg)' },
                    { transform: `translate(${Math.random() * 100 - 50}px, ${window.innerHeight}px) rotate(${Math.random() * 360}deg)` }
                ],
                options: {
                    duration: 2000 + Math.random() * 3000,
                    easing: 'cubic-bezier(0.4, 0, 0.2, 1)'
                }
            },
            stars: {
                keyframes: [
                    { transform: 'scale(0) rotate(0deg)', opacity: 1 },
                    { transform: 'scale(1) rotate(360deg)', opacity: 0 }
                ],
                options: {
                    duration: 1500,
                    easing: 'ease-out'
                }
            },
            hearts: {
                keyframes: [
                    { transform: 'scale(0) translateY(0)', opacity: 1 },
                    { transform: 'scale(1) translateY(-100px)', opacity: 0 }
                ],
                options: {
                    duration: 1000,
                    easing: 'ease-out'
                }
            },
            fireworks: {
                keyframes: [
                    { transform: 'scale(0.1)', opacity: 1 },
                    { transform: 'scale(1)', opacity: 0 }
                ],
                options: {
                    duration: 800,
                    easing: 'cubic-bezier(0.2, 0.8, 0.2, 1)'
                }
            },
            bubbles: {
                keyframes: [
                    { transform: 'scale(0.3) translateY(0)', opacity: 0.8 },
                    { transform: 'scale(1) translateY(-100px)', opacity: 0 }
                ],
                options: {
                    duration: 2000,
                    easing: 'ease-out'
                }
            },
            cats: {
                keyframes: [
                    { transform: 'translateX(0) translateY(0) rotate(0deg)', opacity: 1 },
                    { transform: `translateX(${Math.random() * 100 - 50}px) translateY(-100px) rotate(${Math.random() * 360}deg)`, opacity: 0 }
                ],
                options: {
                    duration: 1500,
                    easing: 'ease-out'
                }
            },
            dogs: {
                keyframes: [
                    { transform: 'scale(0.5) translateX(0)', opacity: 1 },
                    { transform: `scale(1) translateX(${Math.random() * 200 - 100}px)`, opacity: 0 }
                ],
                options: {
                    duration: 1200,
                    easing: 'ease-in-out'
                }
            },
            butterflies: {
                keyframes: [
                    { transform: 'translateX(0) translateY(0) rotate(0deg)', opacity: 1 },
                    { transform: `translateX(${Math.random() * 100 - 50}px) translateY(-150px) rotate(${Math.random() * 360}deg)`, opacity: 0 }
                ],
                options: {
                    duration: 2500,
                    easing: 'ease-in-out'
                }
            },
            leaves: {
                keyframes: [
                    { transform: 'rotate(0deg) translateX(0)', opacity: 1 },
                    { transform: `rotate(${Math.random() * 360}deg) translateX(${Math.random() * 200 - 100}px)`, opacity: 0 }
                ],
                options: {
                    duration: 3000,
                    easing: 'ease-in-out'
                }
            },
            snowflakes: {
                keyframes: [
                    { transform: 'translateY(0) rotate(0deg)', opacity: 1 },
                    { transform: `translateY(${window.innerHeight}px) rotate(${Math.random() * 360}deg)`, opacity: 0.3 }
                ],
                options: {
                    duration: 3000,
                    easing: 'linear'
                }
            }
        };

        if (!animations[this.type]) {
            console.error('未知的特效类型:', this.type);
            return;
        }

        const animation = particle.animate(
            animations[this.type].keyframes,
            animations[this.type].options
        );

        animation.onfinish = () => {
            particle.remove();
            const index = this.particles.indexOf(particle);
            if (index > -1) {
                this.particles.splice(index, 1);
            }
        };
    }

    destroy() {
        document.removeEventListener('mousemove', this.boundMouseMove);
        this.particles.forEach(particle => particle.remove());
        this.particles = [];
    }
}

// 添加锁定功能
function lockDiary() {
    // 保存当前编辑状态
    if (!document.getElementById('diaryEditor').classList.contains('hidden')) {
        // 保存编辑器状态到 sessionStorage
        const editorState = {
            title: document.querySelector('.diary-title-input').value,
            content: document.querySelector('.diary-content-input').value,
            mood: document.querySelector('.mood-selector select').value,
            tags: Array.from(document.querySelectorAll('.tags-container .tag')).map(tag => tag.textContent)
        };
        sessionStorage.setItem('editorState', JSON.stringify(editorState));
    }
    
    // 清除认证状态
    sessionStorage.removeItem('authenticated');
    
    // 添加模糊效果
    document.querySelector('main').classList.add('blur-content');
    
    // 重新加载日记列表（显示模糊效果）
    loadDiaries();
    
    // 显示密码输入界面
    showPasswordDialog();
    
    // 添加锁定动画效果
    const lockBtn = document.getElementById('lockBtn');
    lockBtn.classList.add('locked');
    setTimeout(() => lockBtn.classList.remove('locked'), 1000);
} 
/* 基础样式 */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
    line-height: 1.6;
    color: #333;
    background-color: #f5f5f5;
}

/* 顶部导航栏 */
header {
    background-color: #fff;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    padding: 1rem;
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
}

.nav-buttons button {
    padding: 0.5rem 1rem;
    margin-left: 1rem;
    border: none;
    border-radius: 4px;
    background-color: #007bff;
    color: white;
    cursor: pointer;
}

/* 主要内容区域 */
main {
    max-width: 1200px;
    margin: 2rem auto;
    padding: 0 1rem;
}

/* 日记列表样式 */
.search-bar {
    position: relative;
    margin-bottom: 2rem;
    display: flex;
    gap: 1rem;
}

.search-bar input {
    flex: 1;
    padding: 0.8rem 2.5rem 0.8rem 1rem;
    border: 2px solid transparent;
    border-radius: 8px;
    background: white;
    font-size: 1rem;
    transition: all 0.3s ease;
}

.search-bar input:focus {
    border-color: #007bff;
    box-shadow: 0 0 0 3px rgba(0, 123, 255, 0.2);
}

.search-btn {
    padding: 0.8rem 1.5rem;
    background: #007bff;
    color: white;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.search-btn:hover {
    background: #0056b3;
    transform: translateY(-2px);
}

.clear-search-btn {
    position: absolute;
    right: 140px; /* 调整位置，避免与搜索按钮重叠 */
    top: 50%;
    transform: translateY(-50%);
    background: none;
    border: none;
    color: #666;
    cursor: pointer;
    padding: 0.5rem;
    border-radius: 50%;
    transition: all 0.2s ease;
}

.clear-search-btn:hover {
    background: rgba(0, 0, 0, 0.1);
}

/* 搜索结果高亮 */
.highlight {
    background: #fff3cd;
    padding: 0.1em 0.2em;
    border-radius: 3px;
    color: #856404;
    font-weight: bold;
}

/* 无搜索结果样式 */
.no-results {
    text-align: center;
    padding: 3rem;
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    animation: fadeIn 0.5s ease-out;
}

.no-results-icon {
    font-size: 3rem;
    margin-bottom: 1rem;
    animation: bounce 2s infinite;
}

.no-results h3 {
    color: #333;
    margin-bottom: 0.5rem;
}

.no-results p {
    color: #666;
}

/* 搜索动画 */
@keyframes searchShake {
    0%, 100% { transform: translateX(0); }
    25% { transform: translateX(-5px); }
    75% { transform: translateX(5px); }
}

.search-bar input:not(:placeholder-shown) {
    animation: searchShake 0.3s ease-out;
}

.diary-item {
    background-color: #fff;
    padding: 1rem;
    margin-bottom: 1rem;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    cursor: pointer;
}

.diary-date {
    color: #666;
    font-size: 0.9rem;
}

.diary-mood {
    margin: 0.5rem 0;
}

.diary-title {
    margin-bottom: 0.5rem;
}

.diary-tags {
    margin-top: 1rem;
}

.tag {
    background-color: #e9ecef;
    padding: 0.2rem 0.5rem;
    border-radius: 4px;
    margin-right: 0.5rem;
    font-size: 0.9rem;
}

/* 编辑器样式 */
.editor-toolbar {
    background-color: #fff;
    padding: 1rem;
    border-radius: 8px;
    margin-bottom: 1rem;
}

.tool-btn {
    padding: 0.5rem;
    margin-right: 0.5rem;
    border: none;
    background: none;
    cursor: pointer;
}

.editor-content {
    background-color: #fff;
    padding: 1rem;
    border-radius: 8px;
    margin-bottom: 1rem;
}

.diary-title-input {
    width: 100%;
    padding: 0.5rem;
    margin-bottom: 1rem;
    border: 1px solid #ddd;
    border-radius: 4px;
}

.diary-content-input {
    width: 100%;
    min-height: 300px;
    padding: 0.5rem;
    border: 1px solid #ddd;
    border-radius: 4px;
    resize: vertical;
}

/* 工具类 */
.hidden {
    display: none;
}

.active {
    display: block;
}

/* 密码对话框样式 */
.password-dialog {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.8);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1000;
    backdrop-filter: blur(8px);
}

.password-content {
    background: linear-gradient(135deg, #fff, #f0f2f5);
    padding: 2rem;
    border-radius: 16px;
    box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
    text-align: center;
    animation: slideUp 0.3s ease-out;
    max-width: 90%;
    width: 400px;
}

.lock-icon {
    font-size: 3rem;
    margin-bottom: 1rem;
    animation: bounce 2s infinite;
}

.password-content h3 {
    color: #333;
    font-size: 1.5rem;
    margin-bottom: 0.5rem;
}

.subtitle {
    color: #666;
    margin-bottom: 1.5rem;
}

.password-input-container {
    display: flex;
    gap: 0.5rem;
    margin-bottom: 1rem;
}

#passwordInput {
    flex: 1;
    padding: 0.8rem;
    border: 2px solid #e1e4e8;
    border-radius: 8px;
    font-size: 1.2rem;
    text-align: center;
    letter-spacing: 4px;
    transition: all 0.3s ease;
}

#passwordInput:focus {
    border-color: #007bff;
    box-shadow: 0 0 0 3px rgba(0, 123, 255, 0.2);
}

.confirm-btn {
    padding: 0.8rem 1.5rem;
    border: none;
    border-radius: 8px;
    background: #007bff;
    color: white;
    cursor: pointer;
    transition: all 0.3s ease;
}

.confirm-btn:hover:not(.disabled) {
    background: #0056b3;
    transform: translateY(-2px);
}

.confirm-btn.disabled {
    background: #ccc;
    cursor: not-allowed;
}

.confirm-btn.error {
    background: #dc3545;
    animation: shake 0.5s ease-in-out;
}

.error-message {
    color: #dc3545;
    margin-top: 0.5rem;
    min-height: 20px;
}

/* 模糊效果相关样式 */
.blur-content {
    filter: blur(10px);
    pointer-events: none;
    user-select: none;
}

.blur-item {
    opacity: 0.7;
    background: rgba(255, 255, 255, 0.5) !important;
    pointer-events: none;
    position: relative;
    overflow: hidden;
}

.blur-item::before {
    content: '🔒';
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    font-size: 2rem;
    opacity: 0.2;
}

.blur-text {
    display: inline-block;
    background: #e0e0e0;
    color: transparent;
    border-radius: 4px;
    animation: shimmer 2s infinite linear;
    background: linear-gradient(
        90deg,
        #e0e0e0 0%,
        #f0f0f0 50%,
        #e0e0e0 100%
    );
    background-size: 200% 100%;
}

/* 动画效果 */
@keyframes shimmer {
    0% {
        background-position: -200% 0;
    }
    100% {
        background-position: 200% 0;
    }
}

@keyframes fadeIn {
    from {
        opacity: 0;
        transform: translateY(10px);
    }
    to {
        opacity: 1;
        transform: translateY(0);
    }
}

/* 密码验证成功后的过渡效果 */
.diary-item {
    transition: all 0.5s ease-out;
}

.diary-item.blur-item {
    transform: scale(0.98);
}

.diary-item:not(.blur-item) {
    transform: scale(1);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

/* 动画效果 */
@keyframes slideUp {
    from {
        transform: translateY(20px);
        opacity: 0;
    }
    to {
        transform: translateY(0);
        opacity: 1;
    }
}

@keyframes bounce {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-10px); }
}

@keyframes shake {
    0%, 100% { transform: translateX(0); }
    25% { transform: translateX(-5px); }
    75% { transform: translateX(5px); }
}

/* 主题样式 */
body.simple {
    background-color: #f5f5f5;
    color: #333;
}

body.rich {
    background: linear-gradient(rgba(0, 0, 0, 0.3), rgba(0, 0, 0, 0.3)), 
                url('data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEASABIAAD/4gHYSUNDX1BST0ZJTEUAAQEAAAHIAAAAAAQwAABtbnRyUkdCIFhZWiAH4AABAAEAAAAAAABhY3NwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAQAA9tYAAQAAAADTLQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAlkZXNjAAAA8AAAACRyWFlaAAABFAAAABRnWFlaAAABKAAAABRiWFlaAAABPAAAABR3dHB0AAABUAAAABRyVFJDAAABZAAAAChnVFJDAAABZAAAAChiVFJDAAABZAAAAChjcHJ0AAABjAAAADxtbHVjAAAAAAAAAAEAAAAMZW5VUwAAAAgAAAAcAHMAUgBHAEJYWVogAAAAAAAAb6IAADj1AAADkFhZWiAAAAAAAABimQAAt4UAABjaWFlaIAAAAAAAACSgAAAPhAAAts9YWVogAAAAAAAA9tYAAQAAAADTLXBhcmEAAAAAAAQAAAACZmYAAPKnAAANWQAAE9AAAApbAAAAAAAAAABtbHVjAAAAAAAAAAEAAAAMZW5VUwAAACAAAAAcAEcAbwBvAGcAbABlACAASQBuAGMALgAgADIAMAAxADb/2wBDABQODxIPDRQSEBIXFRQdHx4eHRoaHSQtJSAyVC08MTAxMTIwPURCNz5GPjIxRU5eYWJiY2Q4Rz1LZXhkZGJkYWP/2wBDAR...');  /* 这里使用了一个渐变叠加在背景图上 */
    background-size: cover;
    background-attachment: fixed;
    color: #fff;
    min-height: 100vh;
}

body.rich .diary-item,
body.rich .editor-content,
body.rich .editor-toolbar,
body.rich #settingsPanel {
    background-color: rgba(255, 255, 255, 0.9) !important;
    backdrop-filter: blur(5px);
    border: 1px solid rgba(255, 255, 255, 0.2);
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

body.rich .diary-item {
    color: #333;
}

body.rich .nav-buttons button {
    background-color: rgba(0, 123, 255, 0.8);
    backdrop-filter: blur(5px);
}

body.rich .nav-buttons button:hover {
    background-color: rgba(0, 123, 255, 1);
}

body.rich input,
body.rich textarea,
body.rich select {
    background-color: rgba(255, 255, 255, 0.9);
    border: 1px solid rgba(255, 255, 255, 0.2);
}

body.rich .tag {
    background-color: rgba(233, 236, 239, 0.9);
    backdrop-filter: blur(5px);
}

body.rich header {
    background-color: rgba(255, 255, 255, 0.9);
    backdrop-filter: blur(10px);
}

/* 标签输入样式 */
.tags-input {
    margin-top: 1rem;
}

.tags-container {
    display: flex;
    flex-wrap: wrap;
    gap: 0.5rem;
    margin-top: 0.5rem;
}

.tag {
    background-color: #e9ecef;
    padding: 0.2rem 0.5rem;
    border-radius: 4px;
    font-size: 0.9rem;
    display: inline-flex;
    align-items: center;
}

.tag::after {
    content: '×';
    margin-left: 0.5rem;
    cursor: pointer;
}

/* 响应式设计 */
@media (max-width: 768px) {
    .nav-buttons button {
        padding: 0.3rem 0.8rem;
        font-size: 0.9rem;
    }
    
    .diary-item {
        padding: 0.8rem;
    }
    
    .editor-toolbar {
        flex-wrap: wrap;
    }
    
    .tool-btn {
        padding: 0.3rem;
    }
}

/* 动画效果 */
.diary-item {
    transition: transform 0.2s ease;
}

.diary-item:hover {
    transform: translateY(-2px);
}

.tool-btn {
    transition: background-color 0.2s ease;
}

.tool-btn:hover {
    background-color: #f0f0f0;
}

/* 录音状态样式 */
.recording {
    background-color: #ff4444 !important;
    animation: pulse 1s infinite;
}

@keyframes pulse {
    0% { opacity: 1; }
    50% { opacity: 0.5; }
    100% { opacity: 1; }
}

/* 媒体预览样式 */
.media-preview {
    max-width: 100%;
    margin: 1rem 0;
    border-radius: 4px;
}

.media-preview img,
.media-preview video {
    max-width: 100%;
    border-radius: 4px;
}

.media-preview audio {
    width: 100%;
}

/* 工具栏图标 */
.icon-image::before {
    content: "📷";
}

.icon-voice::before {
    content: "🎤";
}

.icon-video::before {
    content: "🎥";
}

.icon-emoji::before {
    content: "😊";
}

/* 按钮悬停效果 */
.close-btn {
    padding: 0.5rem 1rem;
    background-color: #f0f0f0;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.2s;
}

.close-btn:hover {
    background-color: #e0e0e0;
}

/* 添加日记项目头部样式 */
.diary-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 0.5rem;
}

/* 删除按钮样式 */
.delete-btn {
    padding: 0.3rem 0.6rem;
    background-color: #dc3545;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.9rem;
    transition: background-color 0.2s;
}

.delete-btn:hover {
    background-color: #c82333;
}

/* 确保删除按钮不会触发日记编辑 */
.diary-item {
    position: relative;
}

.diary-item .delete-btn {
    position: relative;
    z-index: 2;
}

/* 修改日记项目的点击效果 */
.diary-item {
    cursor: pointer;
}

.diary-item:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}

/* 删除按钮悬停时不触发日记项目的悬停效果 */
.diary-item .delete-btn:hover + .diary-content {
    transform: none;
}

/* 设置面板样式 */
#settingsPanel {
    background-color: white;
    padding: 2rem;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.settings-group {
    margin-bottom: 2rem;
}

.settings-group h3 {
    margin-bottom: 1rem;
    color: #333;
}

.reminder-settings {
    display: flex;
    gap: 1rem;
    align-items: center;
}

.reminder-settings input,
.reminder-settings select {
    padding: 0.5rem;
    border: 1px solid #ddd;
    border-radius: 4px;
}

/* 返回按钮样式 */
.back-btn {
    padding: 0.5rem 1rem;
    background-color: #f0f0f0;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    margin-bottom: 1rem;
}

.back-btn:hover {
    background-color: #e0e0e0;
}

/* 密码修改对话框样式 */
.dialog-buttons {
    display: flex;
    gap: 1rem;
    margin-top: 1rem;
}

.dialog-buttons button {
    padding: 0.5rem 1rem;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}

.dialog-buttons button:first-child {
    background-color: #007bff;
    color: white;
}

.dialog-buttons button:last-child {
    background-color: #f0f0f0;
}

.password-content input {
    margin-bottom: 0.5rem;
}

/* 全局动画效果 */
@keyframes fadeIn {
    from { opacity: 0; transform: translateY(20px); }
    to { opacity: 1; transform: translateY(0); }
}

@keyframes slideIn {
    from { transform: translateX(-100%); }
    to { transform: translateX(0); }
}

@keyframes scaleIn {
    from { transform: scale(0.9); opacity: 0; }
    to { transform: scale(1); opacity: 1; }
}

/* 导航栏动画 */
.nav-buttons button {
    transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
    position: relative;
    overflow: hidden;
}

.nav-buttons button:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 8px rgba(0, 123, 255, 0.3);
}

.nav-buttons button::after {
    content: '';
    position: absolute;
    top: 50%;
    left: 50%;
    width: 0;
    height: 0;
    background: rgba(255, 255, 255, 0.2);
    border-radius: 50%;
    transform: translate(-50%, -50%);
    transition: width 0.6s, height 0.6s;
}

.nav-buttons button:active::after {
    width: 300px;
    height: 300px;
    opacity: 0;
}

/* 日记列表动画 */
.diary-item {
    animation: fadeIn 0.5s ease-out;
    transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.diary-items {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 1.5rem;
    padding: 1rem;
}

.diary-item:hover {
    transform: translateY(-5px) scale(1.02);
    box-shadow: 0 8px 16px rgba(0,0,0,0.1);
}

/* 编辑器动画 */
#diaryEditor {
    animation: scaleIn 0.3s ease-out;
}

.editor-toolbar {
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.2);
    transition: all 0.3s ease;
}

.tool-btn {
    transition: all 0.2s ease;
    position: relative;
}

.tool-btn:hover {
    transform: scale(1.1);
    background: rgba(0, 123, 255, 0.1);
}

.tool-btn:active {
    transform: scale(0.95);
}

/* 标签动画 */
.tag {
    transition: all 0.2s ease;
    position: relative;
    cursor: pointer;
}

.tag:hover {
    transform: scale(1.1);
    background: #007bff;
    color: white;
}

.tag::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 123, 255, 0.2);
    border-radius: inherit;
    transform: scale(0);
    transition: transform 0.3s ease;
}

.tag:hover::before {
    transform: scale(1);
}

/* 设置面板动画 */
#settingsPanel {
    animation: slideIn 0.3s ease-out;
    backdrop-filter: blur(10px);
}

.settings-group {
    transition: all 0.3s ease;
    padding: 1rem;
    border-radius: 8px;
    background: rgba(255, 255, 255, 0.1);
    margin-bottom: 1.5rem;
}

.settings-group:hover {
    transform: translateX(10px);
    background: rgba(255, 255, 255, 0.2);
}

/* 密码对话框动画 */
.password-dialog {
    animation: fadeIn 0.3s ease-out;
}

.password-content {
    animation: scaleIn 0.3s ease-out;
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.2);
    background: rgba(255, 255, 255, 0.95);
}

/* 输入框样式增强 */
input, textarea, select {
    transition: all 0.3s ease;
    border: 2px solid transparent;
}

input:focus, textarea:focus, select:focus {
    border-color: #007bff;
    box-shadow: 0 0 0 3px rgba(0, 123, 255, 0.2);
    outline: none;
}

/* 按钮悬浮效果 */
button {
    transition: all 0.3s ease;
    position: relative;
    overflow: hidden;
}

button:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}

/* 富主题增强 */
body.rich {
    background: linear-gradient(135deg, #1a2a6c, #b21f1f, #fdbb2d);
    background-size: 400% 400%;
    animation: gradientBG 15s ease infinite;
}

@keyframes gradientBG {
    0% { background-position: 0% 50%; }
    50% { background-position: 100% 50%; }
    100% { background-position: 0% 50%; }
}

/* 加载动画 */
.loading {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(255, 255, 255, 0.9);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 9999;
}

.loading::after {
    content: '';
    width: 50px;
    height: 50px;
    border: 5px solid #f3f3f3;
    border-top: 5px solid #007bff;
    border-radius: 50%;
    animation: spin 1s linear infinite;
}

@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}

/* 滚动条美化 */
::-webkit-scrollbar {
    width: 8px;
    height: 8px;
}

::-webkit-scrollbar-track {
    background: rgba(0, 0, 0, 0.1);
    border-radius: 4px;
}

::-webkit-scrollbar-thumb {
    background: rgba(0, 123, 255, 0.5);
    border-radius: 4px;
    transition: all 0.3s ease;
}

::-webkit-scrollbar-thumb:hover {
    background: rgba(0, 123, 255, 0.8);
}

/* 表情选择器样式 */
.emoji-picker {
    position: absolute;
    top: 100%;
    left: 0;
    width: 350px;
    background: white;
    border-radius: 8px;
    box-shadow: 0 4px 12px rgba(0,0,0,0.1);
    z-index: 1000;
    padding: 1rem;
    animation: scaleIn 0.2s ease-out;
}

.emoji-tabs {
    display: flex;
    gap: 0.5rem;
    margin-bottom: 1rem;
    border-bottom: 1px solid #eee;
    padding-bottom: 0.5rem;
}

.emoji-tabs button {
    padding: 0.5rem 1rem;
    border: none;
    background: none;
    cursor: pointer;
    border-radius: 4px;
    transition: all 0.2s ease;
}

.emoji-tabs button.active {
    background: #007bff;
    color: white;
}

.emoji-categories {
    display: flex;
    gap: 0.5rem;
    margin-bottom: 1rem;
    overflow-x: auto;
    padding-bottom: 0.5rem;
}

.emoji-categories button {
    padding: 0.3rem 0.8rem;
    border: 1px solid #ddd;
    border-radius: 20px;
    background: none;
    cursor: pointer;
    white-space: nowrap;
    transition: all 0.2s ease;
}

.emoji-categories button:hover {
    background: #f0f0f0;
}

.emoji-grid {
    display: grid;
    grid-template-columns: repeat(8, 1fr);
    gap: 0.5rem;
    max-height: 200px;
    overflow-y: auto;
}

.emoji-item {
    font-size: 1.5rem;
    padding: 0.5rem;
    border: none;
    background: none;
    cursor: pointer;
    border-radius: 4px;
    transition: all 0.2s ease;
}

.emoji-item:hover {
    background: #f0f0f0;
    transform: scale(1.2);
}

.gif-search {
    margin-bottom: 1rem;
    display: flex;
    gap: 0.5rem;
}

.gif-search input {
    flex: 1;
    padding: 0.5rem;
    border: 1px solid #ddd;
    border-radius: 4px;
}

.gif-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 0.5rem;
    max-height: 300px;
    overflow-y: auto;
}

.custom-emoji-upload {
    margin-bottom: 1rem;
    text-align: center;
}

.upload-btn {
    display: inline-block;
    padding: 0.5rem 1rem;
    background: #007bff;
    color: white;
    border-radius: 4px;
    cursor: pointer;
    transition: all 0.2s ease;
}

.upload-btn:hover {
    background: #0056b3;
}

.upload-btn input {
    display: none;
}

.custom-emoji-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 0.5rem;
    max-height: 200px;
    overflow-y: auto;
}

.custom-emoji-item {
    position: relative;
    border-radius: 4px;
    overflow: hidden;
}

.custom-emoji-item img {
    width: 100%;
    height: 100%;
    object-fit: cover;
}

.delete-emoji {
    position: absolute;
    top: 0;
    right: 0;
    background: rgba(0,0,0,0.5);
    color: white;
    border: none;
    padding: 0.2rem 0.4rem;
    cursor: pointer;
    opacity: 0;
    transition: opacity 0.2s ease;
}

.custom-emoji-item:hover .delete-emoji {
    opacity: 1;
}

/* 表情动画 */
@keyframes emojiPop {
    0% { transform: scale(0); }
    50% { transform: scale(1.2); }
    100% { transform: scale(1); }
}

.custom-emoji {
    display: inline-block;
    vertical-align: middle;
    animation: emojiPop 0.3s ease-out;
}

/* 红包特效样式 */
.red-packet {
    position: fixed;
    pointer-events: none;
    z-index: 9999;
    width: 30px;
    height: 40px;
    background-image: url('data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 32"><path fill="%23e74c3c" d="M2,0 h20 a2,2 0 0 1 2,2 v28 a2,2 0 0 1 -2,2 h-20 a2,2 0 0 1 -2,-2 v-28 a2,2 0 0 1 2,-2 z"/><path fill="%23c0392b" d="M1,4 h22 v2 h-22 z"/><path fill="%23f1c40f" d="M8,2 h8 v4 h-8 z"/><rect fill="%23f1c40f" x="10" y="8" width="4" height="6" rx="1"/></svg>');
    background-size: contain;
    background-repeat: no-repeat;
    opacity: 0.8;
    animation: redPacketFloat 0.3s ease-out;
}

@keyframes redPacketFloat {
    0% {
        transform: scale(0) rotate(0deg);
        opacity: 0;
    }
    50% {
        transform: scale(1.2) rotate(180deg);
        opacity: 0.8;
    }
    100% {
        transform: scale(1) rotate(360deg);
        opacity: 0.8;
    }
}

/* 优化红包飘落效果 */
.red-packet::after {
    content: '';
    position: absolute;
    top: -2px;
    left: -2px;
    right: -2px;
    bottom: -2px;
    background: radial-gradient(circle at center, rgba(255,215,0,0.3) 0%, transparent 70%);
    filter: blur(2px);
    animation: glowPulse 1s ease-in-out infinite;
}

@keyframes glowPulse {
    0%, 100% { opacity: 0.5; }
    50% { opacity: 1; }
}

/* 添加红包阴影效果 */
.red-packet {
    filter: drop-shadow(0 2px 4px rgba(0,0,0,0.2));
}

/* 添加交互效果 */
.red-packet:hover {
    transform: scale(1.2) rotate(10deg);
    filter: drop-shadow(0 4px 8px rgba(0,0,0,0.3));
}

/* 优化性能 */
.red-packet {
    will-change: transform;
    transform: translateZ(0);
    backface-visibility: hidden;
}

/* 特效开关样式 */
.mouse-effect-settings {
    display: flex;
    flex-direction: column;
    gap: 1rem;
}

.effect-switch {
    display: flex;
    align-items: center;
    gap: 1rem;
}

.switch {
    position: relative;
    display: inline-block;
    width: 60px;
    height: 34px;
}

.switch input {
    opacity: 0;
    width: 0;
    height: 0;
}

.slider {
    position: absolute;
    cursor: pointer;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: #ccc;
    transition: .4s;
    border-radius: 34px;
}

.slider:before {
    position: absolute;
    content: "";
    height: 26px;
    width: 26px;
    left: 4px;
    bottom: 4px;
    background-color: white;
    transition: .4s;
    border-radius: 50%;
}

input:checked + .slider {
    background-color: #2196F3;
}

input:checked + .slider:before {
    transform: translateX(26px);
}

/* 各种特效样式 */
.mouse-effect {
    position: fixed;
    pointer-events: none;
    z-index: 9999;
    background-size: contain;
    background-repeat: no-repeat;
    filter: drop-shadow(0 2px 4px rgba(0,0,0,0.2));
}

/* 星星特效 */
.mouse-effect.stars {
    width: 20px;
    height: 20px;
    background-image: url('data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path fill="%23FFD700" d="M12 0l3.09 9.26L24 12l-8.91 2.74L12 24l-3.09-9.26L0 12l8.91-2.74z"/></svg>');
    background-size: contain;
    filter: drop-shadow(0 0 2px gold);
}

/* 爱心特效 */
.mouse-effect.hearts {
    width: 20px;
    height: 20px;
    background-image: url('data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path fill="%23FF69B4" d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"/></svg>');
    background-size: contain;
}

/* 其他特效样式... */

/* 鼠标特效基础样式 */
.mouse-effect {
    position: fixed;
    pointer-events: none;
    z-index: 9999;
    background-size: contain;
    background-repeat: no-repeat;
    filter: drop-shadow(0 2px 4px rgba(0,0,0,0.2));
}

/* 特效开关样式优化 */
.effect-switch {
    margin-bottom: 1rem;
}

#effectSelector {
    width: 100%;
    padding: 0.5rem;
    border-radius: 4px;
    border: 1px solid #ddd;
    margin-top: 0.5rem;
    cursor: pointer;
    transition: all 0.3s ease;
}

#effectSelector:disabled {
    opacity: 0.5;
    cursor: not-allowed;
}

#effectSelector:not(:disabled):hover {
    border-color: #007bff;
}

/* 特效选项样式 */
#effectSelector option {
    padding: 0.5rem;
}

/* 锁定按钮样式 */
#lockBtn {
    background: none;
    border: none;
    font-size: 1.5rem;
    padding: 0.5rem;
    cursor: pointer;
    transition: all 0.3s ease;
    position: relative;
    overflow: hidden;
}

#lockBtn:hover {
    transform: scale(1.1);
}

#lockBtn.locked {
    animation: lockEffect 1s ease-out;
}

/* 锁定按钮动画 */
@keyframes lockEffect {
    0% {
        transform: scale(1);
    }
    50% {
        transform: scale(1.2) rotate(10deg);
    }
    100% {
        transform: scale(1) rotate(0deg);
    }
}

/* 锁定时的过渡动画 */
.blur-content {
    transition: filter 0.3s ease-out;
}

/* 优化密码对话框动画 */
.password-dialog {
    animation: fadeIn 0.3s ease-out;
}

.password-content {
    animation: slideUp 0.3s ease-out;
}

/* 锁定按钮提示 */
#lockBtn::after {
    content: '锁定日记';
    position: absolute;
    bottom: -30px;
    left: 50%;
    transform: translateX(-50%);
    background: rgba(0, 0, 0, 0.8);
    color: white;
    padding: 0.3rem 0.6rem;
    border-radius: 4px;
    font-size: 0.8rem;
    opacity: 0;
    visibility: hidden;
    transition: all 0.3s ease;
}

#lockBtn:hover::after {
    opacity: 1;
    visibility: visible;
    bottom: -25px;
}

/* 锁定状态指示 */
.nav-buttons {
    position: relative;
}

#lockBtn::before {
    content: '';
    position: absolute;
    top: 0;
    right: 0;
    width: 8px;
    height: 8px;
    background: #4CAF50;
    border-radius: 50%;
    opacity: 0;
    transition: opacity 0.3s ease;
}

body:not(.authenticated) #lockBtn::before {
    opacity: 1;
    background: #f44336;
} 
