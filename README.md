<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Channel Scanning & File Architecture</title>
    <style>
        :root {
            --bg-main: #0f172a;
            --bg-card: #1e293b;
            --bg-card-hover: #24334d;
            --border-color: #334155;
            --text-primary: #f8fafc;
            --text-secondary: #94a3b8;
            --text-muted: #64748b;
            
            --color-working: #10b981;
            --color-working-bg: rgba(16, 185, 129, 0.12);
            --color-block: #f59e0b;
            --color-block-bg: rgba(245, 158, 11, 0.12);
            --color-dead: #ef4444;
            --color-dead-bg: rgba(239, 68, 68, 0.12);
            --color-blue: #38bdf8;
            --color-blue-bg: rgba(56, 189, 248, 0.12);
            --color-purple: #a855f7;
            --color-purple-bg: rgba(168, 85, 247, 0.12);
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg-main);
            color: var(--text-primary);
            line-height: 1.6;
            padding: 2rem 1rem;
            min-height: 100vh;
        }

        .container {
            max-width: 1100px;
            margin: 0 auto;
        }

        /* Header */
        header {
            text-align: center;
            margin-bottom: 3rem;
            padding-bottom: 2rem;
            border-bottom: 1px solid var(--border-color);
        }

        .header-badge {
            display: inline-flex;
            align-items: center;
            gap: 0.5rem;
            background: var(--color-blue-bg);
            color: var(--color-blue);
            border: 1px solid rgba(56, 189, 248, 0.3);
            padding: 0.35rem 1rem;
            border-radius: 9999px;
            font-size: 0.85rem;
            font-weight: 600;
            letter-spacing: 0.05em;
            text-transform: uppercase;
            margin-bottom: 1rem;
        }

        h1 {
            font-size: 2.25rem;
            font-weight: 800;
            letter-spacing: -0.025em;
            color: #ffffff;
            margin-bottom: 0.75rem;
        }

        .subtitle {
            color: var(--text-secondary);
            font-size: 1.1rem;
            max-width: 650px;
            margin: 0 auto;
        }

        /* Workflow Pipeline Flowchart */
        .section-title {
            font-size: 1.25rem;
            font-weight: 700;
            margin-bottom: 1.25rem;
            display: flex;
            align-items: center;
            gap: 0.6rem;
            color: var(--text-primary);
        }

        .section-title svg {
            color: var(--color-blue);
        }

        .pipeline-wrapper {
            background: var(--bg-card);
            border: 1px solid var(--border-color);
            border-radius: 1rem;
            padding: 2rem;
            margin-bottom: 3rem;
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.3);
        }

        .pipeline-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 1.5rem;
            position: relative;
        }

        /* File Architecture Cards Grid */
        .cards-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
            gap: 1.25rem;
            margin-bottom: 3rem;
        }

        .arch-card {
            background: var(--bg-card);
            border: 1px solid var(--border-color);
            border-radius: 0.85rem;
            padding: 1.5rem;
            transition: all 0.2s ease;
            position: relative;
            overflow: hidden;
        }

        .arch-card:hover {
            transform: translateY(-3px);
            border-color: #475569;
            background: var(--bg-card-hover);
        }

        .arch-card::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 4px;
        }

        .arch-card.all-folder::before { background: var(--text-muted); }
        .arch-card.full-name::before { background: var(--color-purple); }
        .arch-card.working-file::before { background: var(--color-working); }
        .arch-card.block-file::before { background: var(--color-block); }

        .card-header {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            margin-bottom: 1rem;
        }

        .card-icon {
            width: 42px;
            height: 42px;
            border-radius: 0.5rem;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .all-folder .card-icon { background: rgba(100, 116, 139, 0.2); color: var(--text-secondary); }
        .full-name .card-icon { background: var(--color-purple-bg); color: var(--color-purple); }
        .working-file .card-icon { background: var(--color-working-bg); color: var(--color-working); }
        .block-file .card-icon { background: var(--color-block-bg); color: var(--color-block); }

        .tag {
            font-size: 0.75rem;
            font-weight: 700;
            padding: 0.2rem 0.6rem;
            border-radius: 0.375rem;
            text-transform: uppercase;
            letter-spacing: 0.05em;
        }

        .tag-gray { background: rgba(148, 163, 184, 0.15); color: #cbd5e1; }
        .tag-purple { background: var(--color-purple-bg); color: #c084fc; }
        .tag-green { background: var(--color-working-bg); color: #34d399; }
        .tag-amber { background: var(--color-block-bg); color: #fbbf24; }

        .card-title {
            font-size: 1.1rem;
            font-weight: 700;
            margin-bottom: 0.35rem;
            font-family: monospace;
            color: #ffffff;
        }

        .card-formula {
            font-size: 0.85rem;
            color: var(--text-secondary);
            margin-bottom: 1rem;
            padding-bottom: 0.75rem;
            border-bottom: 1px solid rgba(255, 255, 255, 0.08);
        }

        .pill-group {
            display: flex;
            flex-wrap: wrap;
            gap: 0.4rem;
        }

        .status-pill {
            display: inline-flex;
            align-items: center;
            gap: 0.35rem;
            font-size: 0.75rem;
            font-weight: 600;
            padding: 0.25rem 0.5rem;
            border-radius: 0.25rem;
        }

        .pill-working { background: var(--color-working-bg); color: var(--color-working); border: 1px solid rgba(16, 185, 129, 0.3); }
        .pill-block { background: var(--color-block-bg); color: var(--color-block); border: 1px solid rgba(245, 158, 11, 0.3); }
        .pill-dead { background: var(--color-dead-bg); color: var(--color-dead); border: 1px solid rgba(239, 68, 68, 0.3); }

        /* Data Matrix Table */
        .table-wrapper {
            background: var(--bg-card);
            border: 1px solid var(--border-color);
            border-radius: 0.85rem;
            overflow: hidden;
            margin-bottom: 3rem;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
        }

        table {
            width: 100%;
            border-collapse: collapse;
            text-align: left;
            font-size: 0.925rem;
        }

        th {
            background: rgba(15, 23, 42, 0.6);
            padding: 1rem 1.25rem;
            font-weight: 600;
            color: var(--text-secondary);
            border-bottom: 1px solid var(--border-color);
            text-transform: uppercase;
            font-size: 0.75rem;
            letter-spacing: 0.05em;
        }

        td {
            padding: 1rem 1.25rem;
            border-bottom: 1px solid rgba(51, 65, 85, 0.5);
            color: var(--text-primary);
        }

        tr:last-child td {
            border-bottom: none;
        }

        tr:hover td {
            background: rgba(255, 255, 255, 0.02);
        }

        .code-name {
            font-family: SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
            background: rgba(0, 0, 0, 0.3);
            padding: 0.25rem 0.5rem;
            border-radius: 0.25rem;
            color: var(--color-blue);
            font-size: 0.85rem;
            border: 1px solid rgba(56, 189, 248, 0.2);
        }

        /* Example Code Block / Practical Naming */
        .example-box {
            background: var(--bg-card);
            border: 1px solid var(--border-color);
            border-radius: 0.85rem;
            padding: 1.5rem;
            margin-bottom: 3rem;
        }

        .example-tree {
            font-family: SFMono-Regular, Menlo, Monaco, Consolas, monospace;
            font-size: 0.9rem;
            line-height: 1.8;
            color: var(--text-secondary);
            background: #090d16;
            padding: 1.25rem;
            border-radius: 0.5rem;
            border: 1px solid rgba(255, 255, 255, 0.05);
            overflow-x: auto;
        }

        .tree-folder { color: #cbd5e1; font-weight: bold; }
        .tree-file-raw { color: #94a3b8; }
        .tree-file-scanned { color: #c084fc; font-weight: 600; }
        .tree-file-working { color: #34d399; font-weight: 600; }
        .tree-file-block { color: #fbbf24; font-weight: 600; }
        .tree-comment { color: #64748b; font-style: italic; }

        /* Footer */
        footer {
            text-align: center;
            color: var(--text-muted);
            font-size: 0.85rem;
            padding-top: 1rem;
            border-top: 1px solid var(--border-color);
        }

        /* SVG Icon Utilities */
        .icon-inline {
            vertical-align: middle;
            margin-right: 0.25rem;
        }
    </style>
</head>
<body>

<div class="container">

    <!-- Header Section -->
    <header>
        <div class="header-badge">
            <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polygon points="12 2 2 7 12 12 22 7 12 2"></polygon><polyline points="2 17 12 22 22 17"></polyline><polyline points="2 12 12 17 22 12"></polyline></svg>
            Specification & Structure Standard
        </div>
        <h1>Channel Scan File Architecture</h1>
        <p class="subtitle">Systematic workflow and naming convention matrix for pre-scan master lists and post-scan channel categorization.</p>
    </header>

    <!-- File Hierarchy Cards -->
    <div class="section-title">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"></path></svg>
        Target Outputs & Output Logic
    </div>

    <div class="cards-grid">
        
        <!-- Raw Full Folder -->
        <div class="arch-card all-folder">
            <div class="card-header">
                <div class="card-icon">
                    <svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"></path></svg>
                </div>
                <span class="tag tag-gray">Raw / Unchecked</span>
            </div>
            <div class="card-title">Full Channels Folder</div>
            <div class="card-formula">No Scan Operation Applied</div>
            <div class="pill-group">
                <span class="status-pill pill-working">● Working</span>
                <span class="status-pill pill-block">● Block</span>
                <span class="status-pill pill-dead">● Dead</span>
            </div>
        </div>

        <!-- Scanned File -->
        <div class="arch-card full-name">
            <div class="card-header">
                <div class="card-icon">
                    <svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"></path><polyline points="14 2 14 8 20 8"></polyline><line x1="16" y1="13" x2="8" y2="13"></line><line x1="16" y1="17" x2="8" y2="17"></line></svg>
                </div>
                <span class="tag tag-purple">Scanned Master</span>
            </div>
            <div class="card-title">Full Name</div>
            <div class="card-formula">Scanned = Working + Block</div>
            <div class="pill-group">
                <span class="status-pill pill-working">● Working</span>
                <span class="status-pill pill-block">● Block</span>
            </div>
        </div>

        <!-- Working File -->
        <div class="arch-card working-file">
            <div class="card-header">
                <div class="card-icon">
                    <svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"></path><polyline points="22 4 12 14.01 9 11.01"></polyline></svg>
                </div>
                <span class="tag tag-green">Filtered</span>
            </div>
            <div class="card-title">Full Name + 1</div>
            <div class="card-formula">Active Streamable Channels</div>
            <div class="pill-group">
                <span class="status-pill pill-working">● Working Only</span>
            </div>
        </div>

        <!-- Block File -->
        <div class="arch-card block-file">
            <div class="card-header">
                <div class="card-icon">
                    <svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"></rect><path d="M7 11V7a5 5 0 0 1 10 0v4"></path></svg>
                </div>
                <span class="tag tag-amber">Restricted</span>
            </div>
            <div class="card-title">Full Name + 2</div>
            <div class="card-formula">Geo-Blocked / Restricted</div>
            <div class="pill-group">
                <span class="status-pill pill-block">● Block Only</span>
            </div>
        </div>

    </div>

    <!-- Data Matrix Breakdown Table -->
    <div class="section-title">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="8" y1="6" x2="21" y2="6"></line><line x1="8" y1="12" x2="21" y2="12"></line><line x1="8" y1="18" x2="21" y2="18"></line><line x1="3" y1="6" x2="3.01" y2="6"></line><line x1="3" y1="12" x2="3.01" y2="12"></line><line x1="3" y1="18" x2="3.01" y2="18"></line></svg>
        Channel Classification Matrix
    </div>

    <div class="table-wrapper">
        <table>
            <thead>
                <tr>
                    <th>Target Object</th>
                    <th>Scan Status</th>
                    <th>Formula / Composition</th>
                    <th>Included Channel Types</th>
                    <th>Primary Purpose</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td><strong>Full Channels Folder</strong></td>
                    <td><span class="tag tag-gray">No Scan</span></td>
                    <td><span class="code-name">Working + Block + Dead</span></td>
                    <td>
                        <span class="status-pill pill-working">Working</span>
                        <span class="status-pill pill-block">Block</span>
                        <span class="status-pill pill-dead">Dead</span>
                    </td>
                    <td>Complete raw source backup before verification.</td>
                </tr>
                <tr>
                    <td><strong>Scan File</strong> (Full Name)</td>
                    <td><span class="tag tag-purple">Scanned</span></td>
                    <td><span class="code-name">Working + Block</span></td>
                    <td>
                        <span class="status-pill pill-working">Working</span>
                        <span class="status-pill pill-block">Block</span>
                    </td>
                    <td>Main verified output file (Dead streams discarded).</td>
                </tr>
                <tr>
                    <td><strong>Full Name + 1</strong></td>
                    <td><span class="tag tag-green">Filtered Active</span></td>
                    <td><span class="code-name">Working</span></td>
                    <td>
                        <span class="status-pill pill-working">Working</span>
                    </td>
                    <td>Production playlist for immediate playback/streaming.</td>
                </tr>
                <tr>
                    <td><strong>Full Name + 2</strong></td>
                    <td><span class="tag tag-amber">Filtered Blocked</span></td>
                    <td><span class="code-name">Block</span></td>
                    <td>
                        <span class="status-pill pill-block">Block</span>
                    </td>
                    <td>Geo-restricted / auth-required channels log.</td>
                </tr>
            </tbody>
        </table>
    </div>

    <!-- Concrete Naming Convention Example -->
    <div class="section-title">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="16 18 22 12 16 6"></polyline><polyline points="8 6 2 12 8 18"></polyline></svg>
        Practical Naming Example
    </div>

    <div class="example-box">
        <p style="color: var(--text-secondary); margin-bottom: 1rem; font-size: 0.95rem;">
            Assuming your base project or category name is <strong style="color:#fff;">"US_Sports"</strong>, the files and folder hierarchy should be organized as follows:
        </p>
        <div class="example-tree">
<span class="tree-folder">📁 US_Sports_Full_Channels/</span>                   <span class="tree-comment">← Raw Folder: Working + Block + Dead</span>
├── 📄 US_Sports_raw.m3u
│
<span class="tree-file-scanned">📄 US_Sports.m3u</span>                                <span class="tree-comment">← Full Name: Working + Block (Scanned)</span>
<span class="tree-file-working">📄 US_Sports_1.m3u</span>                              <span class="tree-comment">← Full Name + 1: Working Only</span>
<span class="tree-file-block">📄 US_Sports_2.m3u</span>                              <span class="tree-comment">← Full Name + 2: Block Only</span>
        </div>
    </div>

    <!-- Footer -->
    <footer>
        <p>Channel Management Workflow & Standard Naming Logic Document</p>
    </footer>

</div>

</body>
</html>
