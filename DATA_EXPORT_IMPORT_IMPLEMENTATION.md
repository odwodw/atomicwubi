# 数据导出导入功能实现总结

## 📋 实现内容

### 1. 功能概述
- ✅ 数据导出功能：导出用户所有数据为JSON文件
- ✅ 数据导入功能：从JSON文件或粘贴数据导入用户数据
- ✅ 完整数据备份：包含学习进度、已掌握字、自定义编码等

### 2. 修改的文件

#### index.html

**修改位置**:

1. **UI按钮添加** (第 674-680 行)
   ```html
   <div class="dict-actions">
     <button style="border-color:var(--cyan);color:var(--cyan)" onclick="openDictImport()">导入编码</button>
     <button style="border-color:var(--gold);color:var(--gold)" onclick="exportUserDict()">导出自定义</button>
     <button style="border-color:var(--green);color:var(--green)" onclick="exportUserData()">导出数据</button>
     <button style="border-color:var(--magenta);color:var(--magenta)" onclick="openDataImport()">导入数据</button>
     <button style="border-color:var(--red);color:var(--red)" onclick="resetUserDict()">重置自定义</button>
   </div>
   ```

2. **数据导入对话框** (第 840-861 行)
   ```html
   <!-- DATA IMPORT DIALOG -->
   <div class="dialog-overlay" id="dataImportDialog">
     <div class="dialog-box wide">
       <h3>导入用户数据</h3>
       <div class="import-hint">
         <strong>注意：</strong>导入数据将覆盖当前用户的所有数据，包括学习进度、已掌握字、成就等。请确保在导入前已备份重要数据。
       </div>
       <div style="margin-bottom:16px">
         <label style="display:block;margin-bottom:8px;font-weight:bold">选择数据文件</label>
         <input type="file" id="dataImportFile" accept=".json" style="width:100%;padding:8px;border:1px solid var(--text-muted);border-radius:var(--radius);background:var(--bg)">
       </div>
       <div style="margin-bottom:16px">
         <label style="display:block;margin-bottom:8px;font-weight:bold">或粘贴数据</label>
         <textarea id="dataImportTextarea" rows="8" placeholder='粘贴JSON数据...' style="width:100%;padding:8px;border:1px solid var(--text-muted);border-radius:var(--radius);background:var(--bg);font-family:var(--font-mono);font-size:.8rem"></textarea>
       </div>
       <div class="import-error" id="dataImportError"></div>
       <div class="dialog-btns">
         <button onclick="closeDataImport()">取消</button>
         <button class="btn-primary" onclick="doDataImport()">导入并覆盖</button>
       </div>
     </div>
   </div>
   ```

3. **数据导出导入函数** (第 5796-5935 行)
   ```javascript
   /* ===========================
      DATA EXPORT/IMPORT
      =========================== */
   function exportUserData(){
     if(!state.currentUser){
       alert('请先登录用户');
       return;
     }
     
     // Get user data
     const accounts = getAllAccounts();
     const userData = accounts[state.currentUser].data;
     
     // Include user dictionary
     const userDict = loadUserDict();
     
     // Create export data
     const exportData = {
       version: '1.0',
       exportDate: new Date().toISOString(),
       username: state.currentUser,
       userData: userData,
       userDict: userDict
     };
     
     // Create JSON string
     const jsonStr = JSON.stringify(exportData, null, 2);
     
     // Create download link
     const blob = new Blob([jsonStr], { type: 'application/json' });
     const url = URL.createObjectURL(blob);
     const a = document.createElement('a');
     a.href = url;
     a.download = `atomicwubi_${state.currentUser}_${new Date().toISOString().split('T')[0]}.json`;
     document.body.appendChild(a);
     a.click();
     document.body.removeChild(a);
     URL.revokeObjectURL(url);
     
     alert('数据导出成功！');
   }

   function openDataImport(){
     document.getElementById('dataImportFile').value = '';
     document.getElementById('dataImportTextarea').value = '';
     document.getElementById('dataImportError').textContent = '';
     document.getElementById('dataImportDialog').classList.add('show');
   }

   function closeDataImport(){
     document.getElementById('dataImportDialog').classList.remove('show');
   }

   function doDataImport(){
     const errorEl = document.getElementById('dataImportError');
     errorEl.textContent = '';
     
     let importData = null;
     
     // Try to get data from file input
     const fileInput = document.getElementById('dataImportFile');
     if (fileInput.files.length > 0) {
       const file = fileInput.files[0];
       const reader = new FileReader();
       
       reader.onload = function(e) {
         try {
           importData = JSON.parse(e.target.result);
           processImportData(importData);
         } catch (error) {
           errorEl.textContent = '文件解析错误：' + error.message;
         }
       };
       
       reader.onerror = function() {
         errorEl.textContent = '文件读取失败';
       };
       
       reader.readAsText(file);
       return;
     }
     
     // Try to get data from textarea
     const textareaData = document.getElementById('dataImportTextarea').value.trim();
     if (textareaData) {
       try {
         importData = JSON.parse(textareaData);
         processImportData(importData);
       } catch (error) {
         errorEl.textContent = '数据解析错误：' + error.message;
       }
       return;
     }
     
     errorEl.textContent = '请选择文件或粘贴数据';
   }

   function processImportData(importData){
     const errorEl = document.getElementById('dataImportError');
     
     // Validate data format
     if (!importData || !importData.version || !importData.userData) {
       errorEl.textContent = '数据格式不正确';
       return;
     }
     
     // Confirm overwrite
     if (!confirm('确定要导入数据并覆盖当前用户的所有数据吗？')) {
       return;
     }
     
     try {
       // Update user data
       const accounts = getAllAccounts();
       if (!accounts[state.currentUser]) {
         errorEl.textContent = '当前用户不存在';
         return;
       }
       
       // Update user data
       accounts[state.currentUser].data = importData.userData;
       saveAllAccounts(accounts);
       
       // Update user dictionary if available
       if (importData.userDict) {
         saveUserDict(importData.userDict);
         applyUserDict();
       }
       
       // Reload user data
       loginAs(state.currentUser, importData.userData);
       
       // Close dialog and show success
       closeDataImport();
       alert('数据导入成功！');
       
     } catch (error) {
       errorEl.textContent = '导入失败：' + error.message;
     }
   }
   ```

### 3. 功能说明

#### 数据导出功能
- ✅ **导出内容**: 包含用户所有数据，包括学习进度、已掌握字、成就、每日经验、自定义编码等
- ✅ **文件格式**: JSON格式，便于查看和编辑
- ✅ **文件名**: `atomicwubi_用户名_日期.json`
- ✅ **文件结构**: 
  ```json
  {
    "version": "1.0",
    "exportDate": "2026-04-01T12:00:00.000Z",
    "username": "用户名",
    "userData": {
      "totalXp": 1000,
      "totalCorrect": 100,
      "knownChars": {"我": true, "的": true},
      "maxComboEver": 20,
      "streak": 5,
      "lastDate": "2026-04-01",
      "achievements": ["first_win", "streak_5"],
      "levelProgress": {"level1": 50},
      "dailyXp": {"2026-04-01": 200}
    },
    "userDict": {
      "自定义字": "编码"
    }
  }
  ```

#### 数据导入功能
- ✅ **导入方式**: 支持文件上传和粘贴数据两种方式
- ✅ **数据验证**: 验证数据格式和版本
- ✅ **覆盖确认**: 导入前确认是否覆盖现有数据
- ✅ **错误处理**: 显示详细的错误信息
- ✅ **自动刷新**: 导入后自动刷新用户数据

### 4. 用户体验

#### 导出流程
1. 用户点击"导出数据"按钮
2. 系统检查用户是否已登录
3. 生成包含所有用户数据的JSON文件
4. 自动下载文件到本地
5. 显示"数据导出成功！"提示

#### 导入流程
1. 用户点击"导入数据"按钮
2. 打开导入对话框
3. 用户选择文件或粘贴数据
4. 系统验证数据格式
5. 确认是否覆盖现有数据
6. 更新用户数据和自定义编码
7. 自动刷新界面
8. 显示"数据导入成功！"提示

### 5. 技术实现

#### 导出实现
```javascript
// 导出数据结构
const exportData = {
  version: '1.0',
  exportDate: new Date().toISOString(),
  username: state.currentUser,
  userData: userData,
  userDict: userDict
};

// 文件下载实现
const blob = new Blob([jsonStr], { type: 'application/json' });
const url = URL.createObjectURL(blob);
const a = document.createElement('a');
a.href = url;
a.download = `atomicwubi_${state.currentUser}_${date}.json`;
a.click();
```

#### 导入实现
```javascript
// 文件读取
const reader = new FileReader();
reader.onload = function(e) {
  importData = JSON.parse(e.target.result);
  processImportData(importData);
};
reader.readAsText(file);

// 数据处理
accounts[state.currentUser].data = importData.userData;
saveAllAccounts(accounts);
saveUserDict(importData.userDict);
loginAs(state.currentUser, importData.userData);
```

### 6. 安全考虑

#### 数据安全
- ✅ **用户验证**: 只有登录用户才能导出数据
- ✅ **数据验证**: 导入时验证数据格式和版本
- ✅ **覆盖确认**: 导入前确认是否覆盖现有数据
- ✅ **错误处理**: 详细的错误信息提示

#### 数据完整性
- ✅ **版本控制**: 数据文件包含版本信息
- ✅ **日期标记**: 导出时记录导出日期
- ✅ **完整备份**: 包含所有用户数据和自定义编码

### 7. 测试建议

#### 功能测试
1. **导出测试**
   - 测试登录用户的数据导出
   - 测试未登录用户的提示
   - 检查导出文件的完整性

2. **导入测试**
   - 测试文件导入功能
   - 测试粘贴数据导入功能
   - 测试数据格式验证
   - 测试覆盖确认功能

3. **错误处理测试**
   - 测试无效文件导入
   - 测试格式错误的数据导入
   - 测试未登录状态的导入

---

**实现状态**: ✅ 已完成  
**测试状态**: ⏳ 待测试  
**文档状态**: ✅ 已完成  

**最后更新**: 2026-04-01  
**作者**: AI Code Assistant
