# Migration Instructions: Multi-Session Architecture

## Overview

This document provides step-by-step instructions to integrate the new multi-session architecture into your existing DQLSathi Eclipse project.

---

## File Locations in Artifact Directory

All new files are in: `C:\Users\SAJAN\.gemini\antigravity\brain\411a73b3-4408-44f8-94a9-06e50024194c\src\`

```
src/
├── main/
│   ├── java/
│   │   └── com/
│   │       └── dqlsathi/
│   │           ├── DqlSathiApplication.java  [REPLACE EXISTING]
│   │           ├── model/
│   │           │   ├── SessionContext.java   [NEW]
│   │           │   └── LoginResult.java      [NEW]
│   │           ├── service/
│   │           │   └── SessionManager.java   [NEW]
│   │           └── ui/
│   │               ├── LoginDialog.java      [NEW]
│   │               ├── SessionTabBar.java    [NEW]
│   │               ├── SessionWorkspace.java [NEW]
│   │               ├── MainToolbar.java      [NEW]
│   │               ├── MainMenuBar.java      [NEW]
│   │               └── EmptyWorkspaceView.java [NEW]
│   └── resources/
│       └── css/
│           └── session-ui.css                [NEW]
```

---

## Step 1: Create New Model Classes

Copy these files to `src/main/java/com/dqlsathi/model/`:

1. **SessionContext.java** - Per-session state container
2. **LoginResult.java** - Login dialog result record

---

## Step 2: Create New Service Classes

Copy this file to `src/main/java/com/dqlsathi/service/`:

1. **SessionManager.java** - Session coordination singleton

---

## Step 3: Create New UI Classes

Copy these files to `src/main/java/com/dqlsathi/ui/`:

1. **LoginDialog.java** - Modal login dialog
2. **SessionTabBar.java** - Session tab bar
3. **SessionWorkspace.java** - Per-session workspace
4. **MainToolbar.java** - Icon toolbar
5. **MainMenuBar.java** - Enhanced menu bar
6. **EmptyWorkspaceView.java** - Placeholder when no sessions open

---

## Step 4: Add CSS Stylesheet

Copy this file to `src/main/resources/css/`:

1. **session-ui.css** - Styles for new components

---

## Step 5: Modify DfcService.java

**File:** `src/main/java/com/dqlsathi/service/DfcService.java`

**Current (Singleton):**
```java
private static DfcService instance;

public static synchronized DfcService getInstance() {
    if (instance == null) {
        instance = new DfcService();
    }
    return instance;
}
```

**Change to (Per-Instance):**
```java
// REMOVE: private static DfcService instance;

// REMOVE: public static synchronized DfcService getInstance() { ... }

// ADD: Public constructor (if currently private)
public DfcService() {
    // Existing initialization code
}
```

**Also update any code that calls `DfcService.getInstance()` to receive the service from `SessionContext` instead.**

---

## Step 6: Modify MetadataCache.java

**File:** `src/main/java/com/dqlsathi/service/MetadataCache.java`

**Current (Singleton):**
```java
private static MetadataCache instance;

public static synchronized MetadataCache getInstance() {
    if (instance == null) {
        instance = new MetadataCache();
    }
    return instance;
}
```

**Change to (Per-Instance):**
```java
// REMOVE: private static DfcService instance;

// REMOVE: public static synchronized MetadataCache getInstance() { ... }

// ADD: Public constructor (if currently private)
public MetadataCache() {
    // Existing initialization code
}

// ADD: Method to clear cache on disconnect
public void clear() {
    // Clear all cached data
}
```

---

## Step 7: Replace DqlSathiApplication.java

**IMPORTANT:** Backup existing file first!

**File:** `src/main/java/com/dqlsathi/DqlSathiApplication.java`

Replace with the new version from the artifact directory. The new version:
- Removes inline ConnectionPanel
- Adds MainMenuBar, MainToolbar, SessionTabBar
- Uses StackPane to switch between session workspaces
- Shows LoginDialog on first launch

---

## Step 8: Find and Replace Singleton Usage

Search your entire project for:
- `DfcService.getInstance()` - Replace with context.getDfcService()
- `MetadataCache.getInstance()` - Replace with context.getMetadataCache()

Common affected files:
- QueryExecutor.java
- ObjectDumper.java
- MetadataService.java
- Any file that accesses DFC

---

## Step 9: Update QueryEditorPanel.java

Add this method if not present:

```java
// Callback for execute action
private Consumer<String> onExecute;

public void setOnExecute(Consumer<String> callback) {
    this.onExecute = callback;
}

// In your execute method, call:
if (onExecute != null) {
    onExecute.accept(getQuery());
}
```

---

## Step 10: Update ResultsPanel.java

Ensure this callback exists:

```java
// Callback for dump request
private Consumer<String> onDumpRequest;

public void setOnDumpRequest(Consumer<String> callback) {
    this.onDumpRequest = callback;
}

// Getter for selected cell value
public String getSelectedCellValue() {
    // Return currently selected cell value
}
```

---

## Step 11: Update DumpTabPane.java

Add this method if not present:

```java
public void closeAllDumpTabs() {
    // Close all dump tabs (keep results tab)
    List<Tab> toRemove = new ArrayList<>();
    for (Tab tab : getTabs()) {
        if (tab != resultsTab) {
            toRemove.add(tab);
        }
    }
    getTabs().removeAll(toRemove);
    dumpTabs.clear();
}
```

---

## Compile and Test Checklist

After copying files and making modifications:

### Build
```bash
cd "d:\dqlSathi Devlopment"
mvn clean compile
```

### Test Cases

1. **First Launch Shows Login Dialog**
   - [ ] Run application
   - [ ] Modal dialog appears
   - [ ] Main window visible but grayed

2. **Login Creates Session Tab**
   - [ ] Enter valid credentials
   - [ ] Tab appears showing user@repository
   - [ ] Workspace shows editor + results

3. **New Session Works**
   - [ ] Click "New Session" toolbar button (or Ctrl+N)
   - [ ] New empty tab appears
   - [ ] Login dialog appears
   - [ ] After login, tab updates with connection name

4. **Multiple Sessions**
   - [ ] Create 2+ sessions
   - [ ] Switch between tabs
   - [ ] Each tab has independent state

5. **Close Tab Works**
   - [ ] Click X on session tab
   - [ ] Tab removed
   - [ ] Another tab activated

6. **Query Execution**
   - [ ] Enter DQL in connected session
   - [ ] Press F5
   - [ ] Results displayed

7. **Keyboard Shortcuts**
   - [ ] Ctrl+N = New Session
   - [ ] Ctrl+W = Close Tab
   - [ ] F5 = Execute Query
   - [ ] Alt+D = Dump Object

---

## Troubleshooting

### Compilation Errors

| Error | Solution |
|-------|----------|
| `DfcService.getInstance() undefined` | Update to use `context.getDfcService()` |
| `MetadataCache.getInstance() undefined` | Update to use `context.getMetadataCache()` |
| `Cannot find symbol: SessionContext` | Add import: `import com.dqlsathi.model.SessionContext;` |
| `Cannot find symbol: SessionManager` | Add import: `import com.dqlsathi.service.SessionManager;` |
| `CSS not loading` | Verify path: `/css/session-ui.css` exists in resources |

### Runtime Errors

| Error | Solution |
|-------|----------|
| `NullPointerException in SessionWorkspace` | Check QueryEditorPanel has setOnExecute method |
| `No results displayed` | Check DfcService connection is from SessionContext |
| `Login dialog not showing` | Check Platform.runLater is calling showFirstLaunchLogin |

---

## Questions

Please share:
1. Compilation errors you encounter
2. Screenshots of any runtime issues
3. Any behavior that differs from expected

I'll help debug and refine the implementation!
