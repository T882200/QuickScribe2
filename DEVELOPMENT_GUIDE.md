# QuickScribe Development Guide

מדריך מפורט להוספת פיצ'רים חדשים לאפליקציית QuickScribe.

---

## 📁 מבנה הפרויקט

```
QuickScribe2/
├── index.html                    # קובץ אחד מכיל הכל (HTML, CSS, JavaScript)
├── README.md                     # תיעוד למשתמשים
├── FEATURE_SUGGESTIONS.md        # הצעות לפיצ'רים עתידיים
├── DEVELOPMENT_GUIDE.md          # המדריך הזה
└── .nojekyll                     # קובץ להגדרות GitHub Pages
```

**הערה חשובה**: זהו פרויקט **single-file** - כל הקוד נמצא ב-`index.html`.

---

## 🎯 איך להוסיף פיצ'ר חדש

### שלב 1: תכנון

לפני שמתחילים לקוד, שאל את עצמך:

1. **האם הפיצ'ר צריך Backend?**
   - ❌ אם כן - לא מתאים לפרויקט זה (client-side only)
   - ✅ אם לא - המשך

2. **האם צריך API חיצוני?**
   - ✅ Groq API (כבר מחובר) - מצוין!
   - ✅ API חינמי אחר - בדוק תיעוד
   - ❌ API בתשלום - שקול חלופה

3. **איפה הפיצ'ר צריך להיות?**
   - CSS (עיצוב)
   - HTML (UI/מבנה)
   - JavaScript (לוגיקה)

### שלב 2: יצירת Branch

```bash
# צור branch חדש עם השם הנכון
git checkout -b claude/your-feature-name-ig7kH

# הערה: ה-branch חייב להתחיל ב-"claude/" ולהסתיים ב-session ID
```

### שלב 3: מיקום הקוד ב-`index.html`

הקובץ מאורגן כך:

```html
<!DOCTYPE html>
<html>
  <head>
    <!-- 1. CSS Styles (שורות 15-540) -->
    <style>
      /* כאן מוסיפים סגנונות חדשים */
    </style>
  </head>

  <body>
    <!-- 2. HTML Structure (שורות 542-780) -->
    <!-- כאן מוסיפים אלמנטי UI חדשים -->

    <!-- 3. JavaScript Logic (שורות 782-2000+) -->
    <script>
      /* כאן מוסיפים פונקציות חדשות */
    </script>
  </body>
</html>
```

---

## 📖 דוגמאות מעשיות

### דוגמה 1: הוספת Export Format חדש (קל)

#### מה צריך:
1. פונקציה שמייצרת את הפורמט
2. כפתור ב-Download Dialog
3. Event handler

#### איפה לשים:

**JavaScript (ליד שורה 1580):**
```javascript
// Convert to XML format (דוגמה)
function convertToXML() {
  if (!transcriptionResult || !transcriptionResult.segments) {
    return '';
  }

  let xml = '<?xml version="1.0" encoding="UTF-8"?>\n';
  xml += '<transcription>\n';
  xml += '  <metadata>\n';
  xml += `    <version>${APP_VERSION}</version>\n`;
  xml += `    <date>${new Date().toISOString()}</date>\n`;
  xml += '  </metadata>\n';
  xml += '  <segments>\n';

  transcriptionResult.segments.forEach((segment, index) => {
    xml += '    <segment>\n';
    xml += `      <id>${index + 1}</id>\n`;
    xml += `      <start>${segment.start}</start>\n`;
    xml += `      <end>${segment.end}</end>\n`;
    xml += `      <text>${segment.text.trim()}</text>\n`;
    xml += '    </segment>\n';
  });

  xml += '  </segments>\n';
  xml += '</transcription>';

  return xml;
}
```

**HTML - עדכון Download Dialog (שורה ~1488):**
```html
<button class="control-button" id="downloadXml">
  <i class="fas fa-file-code"></i> XML
</button>
```

**JavaScript - Event Handler (שורה ~1560):**
```javascript
document.getElementById("downloadXml").onclick = () => {
  const fileName = document.getElementById("fileName").value.trim() || defaultFileName;
  const content = convertToXML();
  if (content) {
    downloadContent(content, `${fileName}.xml`);
    showMessage("קובץ XML הורד בהצלחה");
  } else {
    showMessage("שגיאה ביצירת קובץ XML");
  }
  downloadPopup.remove();
};
```

---

### דוגמה 2: הוספת Translation Feature (בינוני)

#### מה צריך:
1. UI - כפתור תרגום
2. Popup לבחירת שפה
3. קריאה ל-Groq API
4. הצגת תוצאה

#### Implementation:

**CSS (ליד שורה 100):**
```css
.translate-button {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  padding: 10px 15px;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.translate-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(102, 126, 234, 0.4);
}
```

**HTML - הוסף Popup (אחרי summaryPopup):**
```html
<div id="translatePopup" class="popup">
  <div class="popup-content">
    <h2>תרגם תמלול</h2>
    <div style="margin-bottom: 15px;">
      <label for="targetLanguage">בחר שפת יעד:</label>
      <select id="targetLanguage" style="width: 100%; margin-top: 5px; padding: 8px;">
        <option value="en">English</option>
        <option value="es">Español</option>
        <option value="fr">Français</option>
        <option value="de">Deutsch</option>
        <option value="ar">العربية</option>
        <option value="ru">Русский</option>
      </select>
    </div>
    <div class="popup-footer">
      <button class="control-button" onclick="startTranslation()">
        <i class="fas fa-language"></i> תרגם
      </button>
      <button class="control-button" onclick="closePopup('translatePopup')">
        ביטול
      </button>
    </div>
  </div>
</div>
```

**JavaScript - פונקציות תרגום:**
```javascript
// ================== Translation Feature ==================

async function translateTranscription() {
  if (!transcriptionResult || !transcriptionResult.segments) {
    showMessage("אין תמלול זמין לתרגום");
    return;
  }
  showPopup('translatePopup');
}

async function startTranslation() {
  const targetLang = document.getElementById("targetLanguage").value;
  const fullText = transcriptionResult.segments.map(s => s.text.trim()).join(' ');

  closePopup('translatePopup');
  showMessage("מתרגם...", 0);

  try {
    const response = await fetch('https://api.groq.com/openai/v1/chat/completions', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${apiKey}`
      },
      body: JSON.stringify({
        model: 'llama-3.3-70b-versatile',
        messages: [{
          role: 'user',
          content: `Translate the following text to ${targetLang}. Return only the translation, no explanations:\n\n${fullText}`
        }],
        temperature: 0.3,
        max_tokens: 4000
      })
    });

    if (!response.ok) {
      throw new Error(`שגיאה בתרגום: ${response.status}`);
    }

    const data = await response.json();
    const translation = data.choices[0].message.content;

    // הצג את התרגום
    showTranslationResult(translation, targetLang);
    showMessage("תרגום הושלם!");

  } catch (error) {
    console.error('Translation error:', error);
    showMessage(`שגיאה בתרגום: ${error.message}`);
  }
}

function showTranslationResult(translation, targetLang) {
  // יצירת popup חדש להצגת התרגום
  const popup = document.createElement('div');
  popup.className = 'popup';
  popup.style.display = 'flex';

  popup.innerHTML = `
    <div class="popup-content">
      <h2>תרגום (${targetLang})</h2>
      <div style="max-height: 400px; overflow-y: auto; padding: 15px; background: #f5f7fa; border-radius: 8px; margin-bottom: 15px;">
        <p style="white-space: pre-wrap;">${translation}</p>
      </div>
      <div class="popup-footer">
        <button class="control-button" onclick="copyTranslation()">
          <i class="fas fa-copy"></i> העתק
        </button>
        <button class="control-button" onclick="downloadTranslation()">
          <i class="fas fa-download"></i> הורד
        </button>
        <button class="control-button" onclick="this.closest('.popup').remove()">
          סגור
        </button>
      </div>
    </div>
  `;

  document.body.appendChild(popup);
}
```

**הוסף כפתור בUI (ליד כפתור "סכם"):**
```html
<button class="control-button" onclick="translateTranscription()">
  <i class="fas fa-language"></i> תרגם
</button>
```

---

### דוגמה 3: Transcription History עם IndexedDB (מתקדם)

#### מה צריך:
1. IndexedDB setup
2. פונקציות שמירה/טעינה
3. UI להצגת היסטוריה
4. חיפוש ומיון

#### Implementation:

**JavaScript - IndexedDB Setup:**
```javascript
// ================== IndexedDB History ==================

let db;

// Initialize database
async function initDatabase() {
  return new Promise((resolve, reject) => {
    const request = indexedDB.open('QuickScribeDB', 1);

    request.onerror = () => reject(request.error);
    request.onsuccess = () => {
      db = request.result;
      resolve(db);
    };

    request.onupgradeneeded = (event) => {
      const db = event.target.result;

      if (!db.objectStoreNames.contains('transcriptions')) {
        const store = db.createObjectStore('transcriptions', {
          keyPath: 'id',
          autoIncrement: true
        });

        // Create indexes
        store.createIndex('date', 'date', { unique: false });
        store.createIndex('fileName', 'fileName', { unique: false });
      }
    };
  });
}

// Save transcription to history
async function saveToHistory() {
  if (!db) await initDatabase();
  if (!transcriptionResult || !transcriptionResult.segments) {
    showMessage("אין תמלול לשמירה");
    return;
  }

  const transaction = db.transaction(['transcriptions'], 'readwrite');
  const store = transaction.objectStore('transcriptions');

  const record = {
    fileName: selectedFile ? selectedFile.name : 'recording',
    date: new Date().toISOString(),
    segments: transcriptionResult.segments,
    language: document.getElementById("transcriptionLanguage").value,
    duration: transcriptionResult.segments[transcriptionResult.segments.length - 1].end
  };

  store.add(record);

  transaction.oncomplete = () => {
    showMessage("נשמר להיסטוריה");
  };

  transaction.onerror = () => {
    showMessage("שגיאה בשמירה להיסטוריה");
  };
}

// Load history
async function loadHistory() {
  if (!db) await initDatabase();

  return new Promise((resolve, reject) => {
    const transaction = db.transaction(['transcriptions'], 'readonly');
    const store = transaction.objectStore('transcriptions');
    const request = store.getAll();

    request.onsuccess = () => {
      resolve(request.result);
    };

    request.onerror = () => {
      reject(request.error);
    };
  });
}

// Show history UI
async function showHistory() {
  const history = await loadHistory();

  const popup = document.createElement('div');
  popup.className = 'popup';
  popup.style.display = 'flex';

  let historyHTML = '';
  if (history.length === 0) {
    historyHTML = '<p style="text-align: center; color: #999;">אין היסטוריה</p>';
  } else {
    history.reverse().forEach((item, index) => {
      const date = new Date(item.date).toLocaleString('he-IL');
      historyHTML += `
        <div style="padding: 10px; margin-bottom: 10px; background: #f5f7fa; border-radius: 8px; cursor: pointer;" onclick="loadHistoryItem(${item.id})">
          <strong>${item.fileName}</strong><br>
          <small>${date} | ${item.segments.length} segments | ${Math.round(item.duration)}s</small>
        </div>
      `;
    });
  }

  popup.innerHTML = `
    <div class="popup-content">
      <h2>היסטוריית תמלולים</h2>
      <div style="max-height: 400px; overflow-y: auto;">
        ${historyHTML}
      </div>
      <div class="popup-footer">
        <button class="control-button" onclick="clearHistory()">
          <i class="fas fa-trash"></i> נקה הכל
        </button>
        <button class="control-button" onclick="this.closest('.popup').remove()">
          סגור
        </button>
      </div>
    </div>
  `;

  document.body.appendChild(popup);
}

// Load specific history item
async function loadHistoryItem(id) {
  const transaction = db.transaction(['transcriptions'], 'readonly');
  const store = transaction.objectStore('transcriptions');
  const request = store.get(id);

  request.onsuccess = () => {
    const item = request.result;
    transcriptionResult = { segments: item.segments };
    updateTranscription();
    showMessage(`נטען: ${item.fileName}`);
    document.querySelectorAll('.popup').forEach(p => p.remove());
  };
}

// Clear all history
async function clearHistory() {
  if (!confirm('האם למחוק את כל ההיסטוריה?')) return;

  const transaction = db.transaction(['transcriptions'], 'readwrite');
  const store = transaction.objectStore('transcriptions');
  store.clear();

  transaction.oncomplete = () => {
    showMessage("ההיסטוריה נמחקה");
    document.querySelectorAll('.popup').forEach(p => p.remove());
  };
}

// Initialize database on page load
initDatabase();
```

**הוסף כפתור בUI:**
```html
<button class="control-button" onclick="showHistory()">
  <i class="fas fa-history"></i> היסטוריה
</button>
```

**הוסף שמירה אוטומטית אחרי תמלול (בפונקציה `transcribe`):**
```javascript
// בסוף הפונקציה transcribe(), אחרי עדכון התמלול:
await saveToHistory();
```

---

## 🎨 גיידליינים לעיצוב

### צבעים

```css
/* Light Theme */
--primary-color: #3498db;
--secondary-color: #2ecc71;
--background-color: #f5f7fa;
--text-color: #34495e;
--card-background: #ffffff;

/* Dark Theme */
--primary-color: #5dade2;
--secondary-color: #58d68d;
--background-color: #1a1a2e;
--text-color: #eee;
--card-background: #16213e;
```

### כפתורים

```css
.control-button {
  background-color: var(--primary-color);
  color: white;
  border: none;
  padding: 10px 15px;
  border-radius: var(--border-radius);
  cursor: pointer;
  transition: var(--transition);
}

.control-button:hover {
  background-color: #2980b9;
  transform: translateY(-2px);
}
```

### Popups

```html
<div id="yourPopup" class="popup">
  <div class="popup-content">
    <h2>כותרת</h2>
    <!-- תוכן -->
    <div class="popup-footer">
      <button class="control-button" onclick="action()">אישור</button>
      <button class="control-button" onclick="closePopup('yourPopup')">ביטול</button>
    </div>
  </div>
</div>
```

---

## 🧪 בדיקות

לפני commit, בדוק:

1. **בדפדפנים שונים:**
   - Chrome
   - Firefox
   - Safari
   - Edge

2. **במצבי נושא:**
   - בהיר
   - כהה

3. **בגדלי מסך:**
   - Desktop
   - Tablet
   - Mobile

4. **Console:**
   - אין שגיאות JavaScript
   - לוגים עובדים

5. **פונקציונליות:**
   - כל הכפתורים עובדים
   - קיצורי מקלדת עובדים
   - הודעות למשתמש מוצגות

---

## 📦 Commit ו-Push

### מבנה Commit Message

```
<type>: <short description>

<detailed description>

Changes:
- Change 1
- Change 2
- Change 3

Benefits:
- Benefit 1
- Benefit 2
```

### דוגמה:

```bash
git add index.html
git commit -m "Add translation feature with Groq API

Enables users to translate transcriptions to multiple languages.

Changes:
- Added translation popup with language selector
- Integrated with Groq LLaMA API
- Added copy and download for translations
- Updated UI with translate button

Benefits:
- Multi-language content creation
- Better accessibility
- Leverages existing Groq integration"

git push -u origin claude/your-feature-name-ig7kH
```

---

## 🚀 Best Practices

### 1. שמור על סדר

```javascript
// ================== Feature Name ==================

// תיאור קצר של מה הפיצ'ר עושה

function yourFeatureFunction() {
  // implementation
}
```

### 2. הוסף Comments

```javascript
// טען את ההיסטוריה מ-IndexedDB
async function loadHistory() {
  // implementation
}
```

### 3. שימוש ב-localStorage

```javascript
// שמירה
localStorage.setItem('feature-setting', value);

// טעינה
const value = localStorage.getItem('feature-setting') || 'default';
```

### 4. הודעות למשתמש

```javascript
showMessage("הפעולה הושלמה בהצלחה");  // Success
showMessage("שגיאה בביצוע הפעולה");      // Error
showMessage("מעבד...", 0);               // Loading (0 = no timeout)
```

### 5. טיפול בשגיאות

```javascript
try {
  // code that might fail
} catch (error) {
  console.error('Error in feature:', error);
  showMessage(`שגיאה: ${error.message}`);
}
```

---

## 📚 משאבים נוספים

### APIs זמינים

1. **Groq API** (כבר מחובר)
   - Whisper (transcription)
   - LLaMA (text generation/summarization)
   - Documentation: https://console.groq.com/docs

2. **Browser APIs**
   - MediaRecorder: הקלטת אודיו
   - Web Audio API: עיבוד אודיו
   - IndexedDB: אחסון מקומי גדול
   - localStorage: אחסון קטן
   - Clipboard API: העתקה

### ספריות מומלצות (CDN)

```html
<!-- Font Awesome (כבר מחובר) -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css" />

<!-- Google Fonts (כבר מחובר) -->
<link href="https://fonts.googleapis.com/css2?family=Heebo:wght@300;400;700&display=swap" rel="stylesheet" />
```

---

## 🐛 Troubleshooting נפוצים

### שגיאה: "Cannot read property of undefined"
```javascript
// רע
const value = object.property.subproperty;

// טוב
const value = object?.property?.subproperty || 'default';
```

### שגיאה: "Failed to fetch"
```javascript
// הוסף timeout ו-error handling
const response = await fetch(url, {
  method: 'POST',
  headers: {...},
  body: JSON.stringify(data),
  signal: AbortSignal.timeout(30000) // 30s timeout
});

if (!response.ok) {
  throw new Error(`HTTP ${response.status}`);
}
```

### בעיות עם localStorage
```javascript
// תמיד השתמש ב-try-catch
try {
  localStorage.setItem('key', 'value');
} catch (e) {
  console.error('localStorage error:', e);
  // fallback to memory storage
}
```

---

## 📞 קבלת עזרה

1. **Console Logs**: פתח Console (F12) ובדוק לוגים
2. **Commit History**: `git log --oneline` לראות שינויים קודמים
3. **Documentation**: קרא את הקוד הקיים - יש דוגמאות לכל דבר

---

## ✅ Checklist לפיצ'ר חדש

- [ ] תכנון הפיצ'ר (מה צריך, איך זה יעבוד)
- [ ] יצירת branch חדש
- [ ] כתיבת הקוד (CSS + HTML + JavaScript)
- [ ] בדיקה בדפדפנים שונים
- [ ] בדיקה במצב כהה/בהיר
- [ ] בדיקה במובייל
- [ ] הוספת comments בקוד
- [ ] commit עם הודעה מפורטת
- [ ] push לremote
- [ ] בדיקה ב-GitHub Pages (אם רלוונטי)

---

**בהצלחה! 🚀**

אם יש שאלות, תמיד אפשר לעיין בקוד הקיים ולראות איך פיצ'רים אחרים מיושמים.
