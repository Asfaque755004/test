# DQLSathi UI Architecture - Task Breakdown

## Phase 1: Multi-Session Foundation (PRIORITY) 🚧 IN PROGRESS
- [x] Create `SessionContext` - holds connection, cache, history, results per session
- [x] Create `SessionManager` - singleton coordinating all sessions
- [x] Create `LoginDialog` - modal dialog for credentials (replaces inline ConnectionPanel)
- [x] Create `SessionTabBar` - horizontal tab bar for session switching
- [x] Create `SessionWorkspace` - container for per-session editor+results
- [x] Create `MainToolbar` - icon toolbar with session/execution/navigation buttons
- [x] Create `MainMenuBar` - enhanced menu with keyboard shortcuts
- [x] Create `session-ui.css` - styling for new components
- [x] Create `EmptyWorkspaceView` - placeholder when no sessions open
- [x] Update `DqlSathiApplication` - new layout without ConnectionPanel
- [ ] Refactor `DfcService` - support multiple instances (per-session) [NEEDS MODIFICATION]
- [ ] Refactor `MetadataCache` - support multiple instances [NEEDS MODIFICATION]
- [ ] Wire first-launch behavior - show LoginDialog on startup
- [ ] Test and validate the integration

## Phase 2: Main Toolbar
- [ ] Create `MainToolbar` with icon groups
- [ ] Session group: New Connection, Disconnect
- [ ] Execution group: Run, Stop
- [ ] Navigation group: History, Navigator toggle
- [ ] Wire toolbar actions to active session

## Phase 3: Repository Navigator
- [ ] Create `RepositoryNavigator` TreeView
- [ ] Implement Types node (dm_type hierarchy)
- [ ] Implement Folders node (dm_folder tree)
- [ ] Add collapsible behavior (resize editor/results)
- [ ] Wire double-click to insert query template

## Phase 4: Enhanced Menu Bar
- [ ] Session menu (New, Connect history, Disconnect, Close Tab)
- [ ] Edit menu (Undo, Redo, Find, Format)
- [ ] Query menu (Execute, Explain, Cancel)
- [ ] View menu (Toggle Navigator, Zoom)
- [ ] Tools menu (Dump, Future modules placeholder)

## Future Phases (Modular Hooks Ready)
- [ ] DQL Script execution
- [ ] API Script execution
- [ ] Reporting module
- [ ] SiteAnalyser integration
