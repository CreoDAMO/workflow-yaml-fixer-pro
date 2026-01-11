# Analysis and Fixed Code

I've analyzed your code and identified several issues that need to be addressed before deployment:

## Issues Found

1. **PayPal buttons never render** - `startPayPalPayment()` is defined but never called when modals open
2. **VS Code extension has syntax errors** - imports placed incorrectly in `extension.ts`
3. **PayPal SDK container ID mismatch** - some containers reference non-existent IDs
4. **Missing error handling** for payment SDK failures
5. **Free limit bypass** - `fixYAML()` continues processing even after hitting free limit

Here is the corrected and complete code:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Workflow YAML Fixer Pro - Ultimate Complete Edition (January 2026)</title>
    <!-- PayPal SDK -->
    <script src="https://www.paypal.com/sdk/js?client-id=YOUR_PAYPAL_CLIENT_ID&currency=USD"></script>
    <!-- Stripe SDK -->
    <script src="https://js.stripe.com/v3/"></script>
    <!-- js-yaml for parsing -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/js-yaml/4.1.0/js-yaml.min.js"></script>
    <!-- html2canvas for graph export -->
    <script src="https://html2canvas.hertzen.com/dist/html2canvas.min.js"></script>
    <style>
        :root {
            --bg-dark: #0f172a;
            --bg-card: #1e293b;
            --bg-input: #334155;
            --text-primary: #f1f5f9;
            --text-secondary: #94a3b8;
            --accent-primary: #6366f1;
            --accent-secondary: #8b5cf6;
            --success: #10b981;
            --warning: #f59e0b;
            --error: #ef4444;
            --border: #475569;
            --accent-gold: #ffd700;
            --accent-orange: #ff8c00;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; }
        
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: var(--bg-dark);
            color: var(--text-primary);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
        }
        
        .container { max-width: 1800px; margin: 0 auto; padding: 20px; flex: 1; display: flex; flex-direction: column; }
        
        .header {
            background: linear-gradient(135deg, #1e1b4b 0%, #312e81 100%);
            padding: 25px 30px;
            border-radius: 12px;
            margin-bottom: 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 15px;
            border: 1px solid rgba(99, 102, 241, 0.3);
            position: relative;
            overflow: hidden;
        }
        
        .header::before {
            content: '';
            position: absolute;
            top: 0; left: -100%; width: 100%; height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255,255,255,0.1), transparent);
            animation: shimmer 3s infinite;
        }
        
        @keyframes shimmer {
            100% { left: 100%; }
        }
        
        .header h1 {
            font-size: 28px;
            background: linear-gradient(135deg, #818cf8, #c084fc);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            position: relative;
            z-index: 1;
        }
        
        .header-actions { display: flex; gap: 10px; flex-wrap: wrap; position: relative; z-index: 1; }

        .tabs {
            display: flex;
            gap: 5px;
            margin-bottom: 15px;
            background: var(--bg-card);
            padding: 8px;
            border-radius: 10px;
            overflow-x: auto;
        }

        .tab {
            padding: 12px 24px;
            background: transparent;
            border: none;
            color: var(--text-secondary);
            cursor: pointer;
            font-size: 14px;
            font-weight: 600;
            border-radius: 8px;
            transition: all 0.2s;
            white-space: nowrap;
        }

        .tab.active { background: var(--accent-primary); color: white; }
        .tab:hover:not(.active) { background: rgba(99, 102, 241, 0.1); color: var(--accent-primary); }

        .main-content {
            display: grid;
            grid-template-columns: 1fr 1fr 350px;
            gap: 20px;
            flex: 1;
            min-height: 0;
        }

        @media (max-width: 1200px) { .main-content { grid-template-columns: 1fr 1fr; } .sidebar-panel { grid-column: span 2; } }
        @media (max-width: 768px) { .main-content { grid-template-columns: 1fr; } .sidebar-panel { grid-column: span 1; } .header { flex-direction: column; text-align: center; } }

        .panel {
            background: var(--bg-card);
            border-radius: 12px;
            border: 1px solid var(--border);
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        .panel-header {
            padding: 15px 20px;
            border-bottom: 1px solid var(--border);
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: rgba(0,0,0,0.2);
        }

        .panel-title { font-size: 16px; font-weight: 600; color: var(--text-primary); display: flex; align-items: center; gap: 10px; }

        .panel-body { flex: 1; padding: 15px; overflow: auto; display: flex; flex-direction: column; }

        .editor-wrapper {
            position: relative;
            flex: 1;
            min-height: 400px;
            background: #0d1117;
            border-radius: 8px;
            overflow: hidden;
            border: 1px solid var(--border);
        }

        .editor-layer {
            position: absolute;
            top: 0; left: 0;
            width: 100%; height: 100%;
            padding: 15px;
            padding-left: 55px;
            font-family: 'Consolas', 'Monaco', monospace;
            font-size: 14px;
            line-height: 1.6;
            white-space: pre;
            overflow: auto;
            tab-size: 2;
        }

        .line-numbers {
            position: absolute;
            top: 0; left: 0;
            width: 45px; height: 100%;
            background: #161b22;
            border-right: 1px solid var(--border);
            padding: 15px 0;
            text-align: right;
            font-family: 'Consolas', monospace;
            font-size: 14px;
            color: #6e7681;
            user-select: none;
            overflow: hidden;
        }

        .line-numbers span { display: block; padding-right: 10px; line-height: 1.6; }

        #input, #output {
            z-index: 2;
            color: transparent;
            background: transparent;
            caret-color: white;
            border: none;
            resize: none;
            outline: none;
            width: calc(100% - 55px);
        }

        .highlight-layer { z-index: 1; pointer-events: none; color: var(--text-primary); }

        .yaml-key { color: #7ee787; }
        .yaml-action { color: #f0883e; font-weight: bold; }
        .yaml-comment { color: #8b949e; font-style: italic; }
        .yaml-string { color: #a5d6ff; }
        .yaml-number { color: #79c0ff; }

        .controls {
            padding: 15px 20px;
            border-top: 1px solid var(--border);
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            background: rgba(0,0,0,0.2);
        }

        button {
            padding: 10px 20px;
            border: none;
            border-radius: 6px;
            font-size: 13px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .btn-primary {
            background: linear-gradient(135deg, var(--accent-primary), var(--accent-secondary));
            color: white;
            flex: 1;
        }
        
        .btn-primary:hover { transform: translateY(-2px); box-shadow: 0 4px 12px rgba(99, 102, 241, 0.4); }
        
        .btn-secondary { background: var(--bg-input); color: var(--text-primary); }
        .btn-secondary:hover { background: #475569; }

        .donate-btn { background: var(--success); color: white; }
        .donate-btn:hover { background: #059669; }

        .upgrade-btn { background: var(--warning); color: #1e293b; }
        .upgrade-btn:hover { background: #d97706; }

        .buy-now-btn {
            background: linear-gradient(135deg, var(--accent-gold), var(--accent-orange));
            color: #000;
            font-weight: bold;
            animation: pulse 2s infinite;
            border: 2px solid #fff;
        }
        
        .buy-now-btn:hover { transform: scale(1.05); box-shadow: 0 0 20px rgba(255, 215, 0, 0.5); }
        
        @keyframes pulse {
            0% { box-shadow: 0 0 0 0 rgba(255, 215, 0, 0.7); }
            70% { box-shadow: 0 0 0 10px rgba(255, 215, 0, 0); }
            100% { box-shadow: 0 0 0 0 rgba(255, 215, 0, 0); }
        }

        #popularActionsContainer { display: flex; flex-wrap: wrap; gap: 5px; }

        .validation-item {
            display: flex;
            align-items: flex-start;
            gap: 10px;
            padding: 10px;
            margin-bottom: 8px;
            background: rgba(0,0,0,0.2);
            border-radius: 6px;
            border-left: 3px solid transparent;
            font-size: 13px;
        }

        .validation-item.error { border-left-color: var(--error); }
        .validation-item.warning { border-left-color: var(--warning); }
        .validation-item.success { border-left-color: var(--success); }
        .validation-item.info { border-left-color: var(--accent-primary); }

        .stats-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 10px; }
        
        .stat-card {
            background: rgba(0,0,0,0.2);
            padding: 15px;
            border-radius: 8px;
            text-align: center;
        }
        
        .stat-value { font-size: 24px; font-weight: bold; color: var(--accent-primary); }
        .stat-label { font-size: 11px; color: var(--text-secondary); text-transform: uppercase; letter-spacing: 0.5px; }

        .status-bar {
            background: var(--bg-card);
            padding: 10px 20px;
            border-top: 1px solid var(--border);
            display: flex;
            justify-content: space-between;
            font-size: 12px;
            color: var(--text-secondary);
        }

        /* Graph Styles */
        .graph-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 40px;
            padding: 20px;
            min-height: 400px;
        }

        .graph-level {
            display: flex;
            justify-content: center;
            gap: 30px;
            flex-wrap: wrap;
        }

        .graph-node {
            background: var(--bg-input);
            border: 2px solid var(--border);
            border-radius: 12px;
            padding: 15px;
            min-width: 200px;
            text-align: center;
            transition: all 0.3s;
            cursor: pointer;
        }

        .graph-node:hover { border-color: var(--accent-primary); transform: translateY(-5px); box-shadow: 0 10px 20px rgba(99,102,241,0.3); }

        .graph-node .title { font-weight: bold; color: var(--accent-secondary); margin-bottom: 8px; }

        .graph-node .meta { font-size: 12px; color: var(--text-secondary); }

        .graph-arrow {
            font-size: 24px;
            color: var(--accent-primary);
            margin: 0 10px;
        }

        /* Dependency Generator Styles */
        .dep-generator {
            display: flex;
            flex-direction: column;
            gap: 15px;
        }

        .dep-generator select {
            padding: 10px;
            background: var(--bg-input);
            border: 1px solid var(--border);
            border-radius: 6px;
            color: var(--text-primary);
        }

        .dep-output {
            background: #0d1117;
            padding: 15px;
            border-radius: 8px;
            border: 1px solid var(--border);
            font-family: monospace;
            font-size: 13px;
            white-space: pre-wrap;
            color: var(--text-primary);
            min-height: 200px;
            overflow: auto;
        }

        /* Template Styles */
        .template-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); gap: 15px; }

        .template-card {
            background: var(--bg-input);
            padding: 15px;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.2s;
            border: 2px solid transparent;
        }

        .template-card:hover { border-color: var(--accent-primary); background: #3f4f63; }

        .template-card h4 { margin-bottom: 10px; color: var(--accent-secondary); }

        .template-card p { font-size: 12px; color: var(--text-secondary); }

        /* Monetization Styles */
        .modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.8);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            backdrop-filter: blur(5px);
        }

        .modal-content {
            background: var(--bg-card);
            padding: 40px;
            border-radius: 16px;
            max-width: 600px;
            width: 90%;
            text-align: center;
            border: 1px solid var(--border);
            box-shadow: 0 25px 50px rgba(0,0,0,0.5);
        }

        .modal-content h2 { margin-bottom: 15px; color: var(--accent-primary); }

        .modal-content p { margin-bottom: 15px; color: var(--text-secondary); line-height: 1.6; }

        .modal-content .price { font-size: 32px; font-weight: bold; color: var(--accent-gold); margin: 20px 0; }

        .modal-content .features { text-align: left; margin: 20px 0; padding: 20px; background: rgba(0,0,0,0.2); border-radius: 8px; }

        .modal-content .features li { margin: 10px 0; color: var(--text-primary); }

        .usage-bar { background: var(--border); height: 8px; border-radius: 4px; overflow: hidden; margin: 10px 0; }

        .usage-fill { background: var(--accent-primary); height: 100%; width: 0%; transition: width 0.5s; }

        .premium-badge { background: linear-gradient(135deg, gold, orange); color: black; padding: 4px 8px; border-radius: 4px; font-size: 11px; font-weight: bold; margin-left: 10px; }

        .premium-badge-small { background: linear-gradient(135deg, gold, orange); color: black; padding: 2px 6px; border-radius: 4px; font-size: 10px; font-weight: bold; }

        .referral-box { background: rgba(0,0,0,0.3); padding: 15px; border-radius: 8px; margin-top: 15px; }

        .referral-input { width: 100%; padding: 10px; background: var(--bg-input); border: 1px solid var(--border); border-radius: 6px; color: var(--text-primary); font-family: monospace; font-size: 12px; margin-top: 10px; }

        .loading-spinner {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 3px solid rgba(255,255,255,0.3);
            border-radius: 50%;
            border-top-color: white;
            animation: spin 1s ease-in-out infinite;
        }

        @keyframes spin { to { transform: rotate(360deg); } }

        .badge-pro { background: linear-gradient(135deg, #667eea, #764ba2); color: white; padding: 3px 8px; border-radius: 4px; font-size: 11px; font-weight: bold; }

        .toast {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background: var(--success);
            color: white;
            padding: 15px 25px;
            border-radius: 8px;
            z-index: 2000;
            animation: slideIn 0.3s ease;
        }

        @keyframes slideIn { from { transform: translateX(100%); opacity: 0; } to { transform: translateX(0); opacity: 1; } }

        .limit-reached-banner {
            background: linear-gradient(135deg, rgba(245, 158, 11, 0.2), rgba(245, 158, 11, 0.1));
            border: 1px solid var(--warning);
            border-radius: 8px;
            padding: 15px;
            margin: 10px 0;
            display: none;
        }

        .limit-reached-banner.show { display: block; }

        .limit-reached-banner h4 { color: var(--warning); margin-bottom: 8px; display: flex; align-items: center; gap: 8px; }

        .limit-reached-banner p { font-size: 13px; color: var(--text-secondary); margin-bottom: 10px; }

        .loading-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(15, 23, 42, 0.9);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 3000;
            flex-direction: column;
            gap: 20px;
        }

        .loading-overlay.show { display: flex; }

        .loading-overlay h3 { color: var(--text-primary); }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Workflow YAML Fixer Pro <span class="premium-badge">Premium Available</span></h1>
            <div class="header-actions">
                <button class="btn-secondary" onclick="importFile()" aria-label="Import YAML file">📂 Import</button>
                <button class="btn-secondary" onclick="exportAll()" aria-label="Export all data">💾 Export All</button>
                <button class="donate-btn" onclick="showModal('donateModal')" aria-label="Support development">💰 Support Development</button>
                <button class="upgrade-btn" onclick="showModal('upgradeModal')" aria-label="Go premium">🚀 Go Premium</button>
                <button class="buy-now-btn" onclick="showModal('buyModal')" aria-label="Buy this tool">💎 Buy This Tool – $750,000</button>
            </div>
        </div>

        <div class="tabs">
            <button class="tab active" onclick="switchTab('editor')" aria-label="Editor and fixer tab">Editor & Fixer</button>
            <button class="tab" onclick="switchTab('graph')" aria-label="Interactive graph tab">Interactive Graph</button>
            <button class="tab" onclick="switchTab('validation')" aria-label="Deep validation tab">Deep Validation</button>
            <button class="tab" onclick="switchTab('templates')" aria-label="Templates tab">Templates</button>
            <button class="tab" onclick="switchTab('dependencies')" aria-label="Dependency generator tab">Dependencies</button>
            <button class="tab" onclick="switchTab('analytics')" aria-label="Analytics tab">Analytics</button>
        </div>

        <div id="editor-tab" class="tab-content active">
            <div class="main-content">
                <div class="panel">
                    <div class="panel-header">
                        <div class="panel-title">Input YAML</div>
                        <div>
                            <div class="usage-bar"><div class="usage-fill" id="usageFill" style="width: 0%;"></div></div>
                            <small><span id="usageText">0/5 free fixes used</span></small>
                            <button class="btn-secondary" style="padding: 5px 10px; font-size: 11px;" onclick="clearInput()" aria-label="Clear input">Clear</button>
                        </div>
                    </div>
                    <div class="panel-body" style="padding: 0;">
                        <div class="editor-wrapper">
                            <div class="line-numbers" id="inputLineNumbers"></div>
                            <div class="highlight-layer editor-layer" id="inputHighlight"></div>
                            <textarea class="editor-layer" id="input" placeholder="Paste GitHub Actions YAML here..." spellcheck="false" oninput="handleInput()" onscroll="syncScroll()" aria-label="YAML input editor"></textarea>
                        </div>
                    </div>
                    <div class="controls">
                        <button class="btn-primary" onclick="fixYAML()" aria-label="Fix YAML">⚡ Fix & Auto-Upgrade Actions</button>
                        <button class="upgrade-btn" onclick="showModal('upgradeModal')" aria-label="Upgrade to premium">Upgrade to Premium</button>
                    </div>
                </div>

                <div class="panel">
                    <div class="panel-header">
                        <div class="panel-title">Fixed Output <span class="badge-pro">PRO</span></div>
                        <div style="display: flex; gap: 5px;">
                            <button class="btn-secondary" style="padding: 5px 10px; font-size: 11px;" onclick="copyOutput()" aria-label="Copy output">Copy</button>
                            <button class="btn-secondary" style="padding: 5px 10px; font-size: 11px;" onclick="downloadYAML()" aria-label="Download YAML">Download</button>
                        </div>
                    </div>
                    <div class="panel-body" style="padding: 0;">
                        <div class="editor-wrapper">
                            <div class="line-numbers" id="outputLineNumbers"></div>
                            <div class="highlight-layer editor-layer" id="outputHighlight"></div>
                            <textarea class="editor-layer" id="output" readonly placeholder="Fixed YAML will appear here..." aria-label="Fixed YAML output"></textarea>
                        </div>
                    </div>
                    <div id="limitBanner" class="limit-reached-banner">
                        <h4>⚠️ Free Limit Reached</h4>
                        <p>You've used all 5 free fixes. Upgrade to Premium for unlimited fixes!</p>
                        <button class="upgrade-btn" onclick="showModal('upgradeModal')" style="width: 100%; justify-content: center;">🚀 Upgrade Now for Unlimited Access</button>
                    </div>
                </div>

                <div class="panel sidebar-panel">
                    <div class="panel-header">
                        <div class="panel-title">Workflow Status</div>
                    </div>
                    <div class="panel-body">
                        <div class="stats-grid">
                            <div class="stat-card"><div class="stat-value" id="statJobs">0</div><div class="stat-label">Jobs</div></div>
                            <div class="stat-card"><div class="stat-value" id="statSteps">0</div><div class="stat-label">Steps</div></div>
                            <div class="stat-card"><div class="stat-value" id="statActions">0</div><div class="stat-label">Actions</div></div>
                            <div class="stat-card"><div class="stat-value" id="statIssues" style="color: var(--success);">0</div><div class="stat-label">Issues</div></div>
                        </div>

                        <div style="margin-top: 20px;">
                            <h4 style="margin-bottom: 10px; font-size: 14px;">Quick Validation</h4>
                            <div id="quickValidation"><div class="validation-item info">Paste YAML to analyze</div></div>
                        </div>

                        <div style="margin-top: 20px;">
                            <h4 style="margin-bottom: 10px; font-size: 14px;">Popular Actions (Live - Jan 2026)</h4>
                            <div id="popularActionsContainer"></div>
                            <div id="versionStatus" style="margin-top: 10px; font-size: 12px; color: var(--success);"></div>
                        </div>

                        <div style="margin-top: 20px;">
                            <h4 style="margin-bottom: 10px; font-size: 14px;">Share & Earn</h4>
                            <div class="referral-box">
                                <p style="font-size: 12px; margin-bottom: 10px;">Generate a referral link and earn rewards!</p>
                                <button class="btn-secondary" style="width: 100%;" onclick="generateReferralLink()" aria-label="Generate referral link">Generate Referral Link</button>
                                <input type="text" id="referralInput" class="referral-input" readonly placeholder="Your referral link will appear here" aria-label="Referral link" style="margin-top: 10px;">
                                <div id="referralStats" style="margin-top: 10px; font-size: 12px; color: var(--text-secondary);"></div>
                            </div>
                        </div>

                        <div style="margin-top: 20px; padding: 15px; background: rgba(99, 102, 241, 0.1); border-radius: 8px; border: 1px solid rgba(99, 102, 241, 0.3);">
                            <h4 style="margin-bottom: 10px; font-size: 14px; color: var(--accent-primary);">💡 Pro Tip</h4>
                            <p style="font-size: 12px; color: var(--text-secondary);">Upgrade to Premium for unlimited fixes, advanced validation, and priority support!</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div id="graph-tab" class="tab-content" style="display:none;">
            <div class="panel" style="flex: 1;">
                <div class="panel-header">
                    <div class="panel-title">Interactive Workflow Graph</div>
                    <div style="display: flex; gap: 10px;">
                        <button class="btn-secondary" onclick="renderGraph()" aria-label="Refresh graph">🔄 Refresh Graph</button>
                        <button class="btn-secondary" onclick="exportGraph()" aria-label="Export graph as image">📷 Export as Image</button>
                    </div>
                </div>
                <div class="panel-body" style="background:#0d1117; padding: 0;">
                    <div class="graph-container" id="graphContainer">
                        <div style="text-align: center; padding: 50px; color: var(--text-secondary);">
                            <p style="font-size: 48px; margin-bottom: 20px;">📊</p>
                            <p>Paste a workflow YAML in the Editor tab and click "Fix" to generate the interactive graph.</p>
                            <p style="margin-top: 10px; font-size: 12px;">The graph shows job dependencies and execution order.</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div id="validation-tab" class="tab-content" style="display:none;">
            <div class="panel" style="flex: 1;">
                <div class="panel-header">
                    <div class="panel-title">Deep Validation & Best Practices</div>
                    <button class="btn-primary" onclick="runDeepValidation()" aria-label="Run full scan">🔍 Run Full Scan</button>
                </div>
                <div class="panel-body" id="validationContainer">
                    <div class="validation-item info">
                        <strong>Analysis Ready:</strong> Enter your YAML and click "Run Full Scan" to check for circular dependencies, deprecated actions, security risks, and best practices.
                    </div>
                    <div style="margin-top: 20px; padding: 20px; background: rgba(0,0,0,0.2); border-radius: 8px;">
                        <h4 style="margin-bottom: 15px;">What We Check:</h4>
                        <ul style="font-size: 13px; color: var(--text-secondary); text-align: left; padding-left: 20px;">
                            <li style="margin: 8px 0;">🔒 Security vulnerabilities (unpinned actions, secrets exposure)</li>
                            <li style="margin: 8px 0;">⚡ Performance optimization (caching strategies, parallel jobs)</li>
                            <li style="margin: 8px 0;">🔄 Circular dependencies between jobs</li>
                            <li style="margin: 8px 0;">📝 Best practices (permissions, concurrency, timeouts)</li>
                            <li style="margin: 8px 0;">🚫 Deprecated actions and outdated patterns</li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>

        <div id="templates-tab" class="tab-content" style="display:none;">
            <div class="panel" style="flex: 1;">
                <div class="panel-header">
                    <div class="panel-title">Workflow Templates</div>
                    <span class="premium-badge-small">New Templates Added</span>
                </div>
                <div class="panel-body">
                    <div class="template-grid" id="templateGrid">
                        <!-- Templates populated by JS -->
                    </div>
                </div>
            </div>
        </div>

        <div id="dependencies-tab" class="tab-content" style="display:none;">
            <div class="panel" style="flex: 1;">
                <div class="panel-header">
                    <div class="panel-title">Dependency Generator</div>
                    <button class="btn-primary" onclick="generateDependencies()" aria-label="Generate dependencies">🔧 Generate Dependencies</button>
                </div>
                <div class="panel-body">
                    <div class="dep-generator">
                        <p style="margin-bottom: 15px; color: var(--text-secondary); font-size: 13px;">Select a package manager to generate dependency files (e.g., requirements.txt, package.json) based on your workflow's actions and steps.</p>
                        <select id="depType" aria-label="Select dependency type">
                            <option value="python">Python (requirements.txt)</option>
                            <option value="node">Node.js (package.json)</option>
                            <option value="java">Java (pom.xml)</option>
                            <option value="go">Go (go.mod)</option>
                            <option value="rust">Rust (Cargo.toml)</option>
                        </select>
                        <div class="dep-output" id="depOutput">Generated dependency file will appear here...</div>
                    </div>
                </div>
            </div>
        </div>

        <div id="analytics-tab" class="tab-content" style="display:none;">
            <div class="panel" style="flex: 1;">
                <div class="panel-header">
                    <div class="panel-title">Usage Analytics & Insights</div>
                </div>
                <div class="panel-body">
                    <div class="stats-grid" style="margin-bottom: 30px;">
                        <div class="stat-card">
                            <div class="stat-value" id="totalFixes">0</div>
                            <div class="stat-label">Total Fixes</div>
                        </div>
                        <div class="stat-card">
                            <div class="stat-value" id="totalJobsFound">0</div>
                            <div class="stat-label">Jobs Analyzed</div>
                        </div>
                        <div class="stat-card">
                            <div class="stat-value" id="totalActionsUpgraded">0</div>
                            <div class="stat-label">Actions Upgraded</div>
                        </div>
                        <div class="stat-card">
                            <div class="stat-value" id="issuesDetected">0</div>
                            <div class="stat-label">Issues Detected</div>
                        </div>
                    </div>
                    
                    <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px;">
                        <div style="background: rgba(0,0,0,0.2); padding: 20px; border-radius: 8px;">
                            <h4 style="margin-bottom: 15px;">📈 Activity Over Time</h4>
                            <div id="activityChart" style="height: 150px; display: flex; align-items: flex-end; gap: 5px; padding-top: 20px;">
                                <!-- Activity bars populated by JS -->
                            </div>
                        </div>
                        <div style="background: rgba(0,0,0,0.2); padding: 20px; border-radius: 8px;">
                            <h4 style="margin-bottom: 15px;">🏆 Most Used Templates</h4>
                            <div id="templateUsage">
                                <p style="font-size: 13px; color: var(--text-secondary);">Start using templates to see stats here!</p>
                            </div>
                        </div>
                    </div>
                    <div style="margin-top: 20px; padding: 20px; background: rgba(99, 102, 241, 0.1); border-radius: 8px; border: 1px solid rgba(99, 102, 241, 0.3);">
                        <h4 style="margin-bottom: 10px; color: var(--accent-primary);">💎 Premium Analytics</h4>
                        <p style="font-size: 13px; color: var(--text-secondary);">Upgrade to unlock detailed analytics, export reports, team collaboration features, and custom branding.</p>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <div class="status-bar">
        <span id="cursorPosition">Ln 1, Col 1</span>
        <span>Spaces: 2 | UTF-8 | YAML | GitHub Actions</span>
        <span id="versionInfo">v2.1.0 | January 2026</span>
    </div>

    <input type="file" id="fileInput" accept=".yml,.yaml" style="display: none;" onchange="handleFileUpload(event)">

    <!-- Loading overlay -->
    <div class="loading-overlay" id="loadingOverlay">
        <div class="loading-spinner"></div>
        <h3>Loading YAML library...</h3>
    </div>
    
    <script>
        // ============================================
        // CHECK IF JS-YAML IS LOADED
        // ============================================
        function checkJsYamlLoaded() {
            return typeof jsyaml !== 'undefined' && jsyaml !== null;
        }

        // Show loading until js-yaml is ready
        function waitForJsYaml() {
            return new Promise((resolve, reject) => {
                if (checkJsYamlLoaded()) {
                    resolve();
                    return;
                }
                
                // Show loading overlay
                document.getElementById('loadingOverlay').classList.add('show');
                
                // Check every 100ms
                const interval = setInterval(() => {
                    if (checkJsYamlLoaded()) {
                        clearInterval(interval);
                        document.getElementById('loadingOverlay').classList.remove('show');
                        resolve();
                    }
                }, 100);
                
                // Timeout after 10 seconds
                setTimeout(() => {
                    clearInterval(interval);
                    document.getElementById('loadingOverlay').classList.remove('show');
                    reject(new Error('js-yaml library failed to load'));
                }, 10000);
            });
        }

        // ============================================
        // SAFE LOCALSTORAGE WRAPPER
        // ============================================
        function safeLocalStorage() {
            try {
                const test = '__storage_test__';
                localStorage.setItem(test, test);
                localStorage.removeItem(test);
                return true;
            } catch (e) {
                console.warn('LocalStorage unavailable (sandboxed environment). Using in-memory storage.');
                return false;
            }
        }

        const hasLocalStorage = safeLocalStorage();

        // ============================================
        // USER STATE & MONETIZATION
        // ============================================
        let user = {
            email: '',
            premium: false,
            fixesUsed: 0,
            totalFixes: 0,
            referrals: 0,
            referralCode: '',
            sessions: [],
            templateUsage: {},
            actionsUpgraded: 0
        };

        function loadUser() {
            if (hasLocalStorage) {
                try {
                    const stored = localStorage.getItem('wfUser');
                    if (stored) {
                        user = { ...user, ...JSON.parse(stored) };
                    }
                } catch (e) {
                    console.error('Error loading user data:', e);
                }
            }
        }

        function saveUser() {
            if (hasLocalStorage) {
                try {
                    localStorage.setItem('wfUser', JSON.stringify(user));
                } catch (e) {
                    console.error('Error saving user data:', e);
                }
            }
        }

        loadUser();

        if (!user.referralCode) {
            user.referralCode = 'REF' + Math.random().toString(36).substr(2, 8).toUpperCase();
            saveUser();
        }

        // ============================================
        // POPULAR ACTIONS DATABASE (UPDATED JAN 2026)
        // ============================================
        const popularActions = [
            { name: 'checkout', repo: 'actions/checkout', current: 'v6.0.1' },
            { name: 'setup-node', repo: 'actions/setup-node', current: 'v6.1.0' },
            { name: 'setup-python', repo: 'actions/setup-python', current: 'v6.1.0' },
            { name: 'setup-java', repo: 'actions/setup-java', current: 'v5.1.0' },
            { name: 'upload-artifact', repo: 'actions/upload-artifact', current: 'v6.0.0' },
            { name: 'download-artifact', repo: 'actions/download-artifact', current: 'v7.0.0' },
            { name: 'cache', repo: 'actions/cache', current: 'v5.0.1' },
            { name: 'docker-login', repo: 'docker/login-action', current: 'v3.6.0' },
            { name: 'aws-credentials', repo: 'aws-actions/configure-aws-credentials', current: 'v5.1.1' },
            { name: 'mdbook', repo: 'peaceiris/actions-mdbook', current: 'v2.0.0' },
            { name: 'setup-go', repo: 'actions/setup-go', current: 'v6.1.0' },
            { name: 'setup-rust', repo: 'actions-rs/toolchain', current: 'v1.0.6' },
            { name: 'github-script', repo: 'actions/github-script', current: 'v8.0.0' },
            { name: 'create-pull-request', repo: 'peter-evans/create-pull-request', current: 'v8.0.0' },
            { name: 'codecov-action', repo: 'codecov/codecov-action', current: 'v5.5.2' }
        ];

        let parsedWorkflow = null;

        // ============================================
        // VERSION FETCHING
        // ============================================
        async function fetchLatestVersions() {
            const status = document.getElementById('versionStatus');
            status.innerHTML = '<span class="loading-spinner"></span> Fetching latest versions...';

            if (!navigator.onLine) {
                status.textContent = '❌ Offline - using cached versions';
                return;
            }

            let updated = false;
            for (const action of popularActions) {
                try {
                    const response = await fetch(`https://api.github.com/repos/${action.repo}/releases/latest`, {
                        headers: {
                            'Accept': 'application/vnd.github+json',
                            'User-Agent': 'Workflow-YAML-Fixer'
                        }
                    });
                    if (response.ok) {
                        const data = await response.json();
                        const latest = data.tag_name;
                        if (latest !== action.current) {
                            action.current = latest;
                            updated = true;
                        }
                    }
                } catch (e) {
                    console.error(`Failed to fetch ${action.repo}:`, e);
                }
                await new Promise(r => setTimeout(r, 100));
            }

            if (updated) populatePopularActions();
            status.textContent = updated ? '✅ Versions updated!' : '✅ All actions up-to-date.';
            setTimeout(() => status.textContent = '', 4000);
        }

        function populatePopularActions() {
            const container = document.getElementById('popularActionsContainer');
            container.innerHTML = '';
            popularActions.slice(0, 10).forEach(a => {
                const tag = document.createElement('span');
                tag.style.cssText = 'background:var(--bg-input);padding:4px 8px;border-radius:4px;font-size:11px;cursor:pointer;margin:2px;transition:all 0.2s;';
                tag.textContent = `${a.name}@${a.current}`;
                tag.onclick = () => insertAction(`${a.repo}@${a.current}`);
                tag.onmouseover = () => tag.style.background = 'var(--accent-primary)';
                tag.onmouseout = () => tag.style.background = 'var(--bg-input)';
                container.appendChild(tag);
            });

            if (!document.getElementById('fetchBtn')) {
                const btn = document.createElement('button');
                btn.id = 'fetchBtn';
                btn.className = 'btn-secondary';
                btn.style.cssText = 'margin-top:10px;width:100%;font-size:11px;padding:8px;';
                btn.innerHTML = '🔄 Fetch Latest Versions';
                btn.onclick = fetchLatestVersions;
                container.parentElement.appendChild(btn);
            }
        }

        function insertAction(action) {
            const ta = document.getElementById('input');
            const pos = ta.selectionStart;
            const text = ta.value;
            const before = text.substring(0, pos);
            const after = text.substring(pos);
            ta.value = before + `\n      uses: ${action}` + after;
            ta.selectionStart = ta.selectionEnd = pos + action.length + 14;
            ta.focus();
            handleInput();
        }

        // ============================================
        // PAYMENT INTEGRATION
        // ============================================
        
        // PayPal buttons rendered flag to prevent double-rendering
        let paypalDonateRendered = false;
        let paypalUpgradeRendered = false;

        // Initialize PayPal buttons in modals
        function initPayPalButtons() {
            // Donate modal PayPal button
            const donateContainer = document.getElementById('paypal-donate-container');
            if (donateContainer && typeof paypal !== 'undefined' && !paypalDonateRendered) {
                try {
                    paypal.Buttons({
                        createOrder: (data, actions) => {
                            return actions.order.create({
                                purchase_units: [{
                                    amount: { value: '5.00' }
                                }]
                            });
                        },
                        onApprove: (data, actions) => {
                            return actions.order.capture().then(details => {
                                showToast('Thank you for your donation! 💖');
                                user.premium = true;
                                saveUser();
                                updateUsageBar();
                            });
                        },
                        onError: (err) => {
                            console.error('PayPal error:', err);
                            showToast('PayPal payment failed. Please try again.', 'error');
                        }
                    }).render('#paypal-donate-container');
                    paypalDonateRendered = true;
                } catch (e) {
                    console.error('Failed to render PayPal donate button:', e);
                    donateContainer.innerHTML = '<p style="color: var(--error); font-size: 12px;">PayPal unavailable</p>';
                }
            }

            // Upgrade modal PayPal button (subscription)
            const upgradeContainer = document.getElementById('paypal-upgrade-container');
            if (upgradeContainer && typeof paypal !== 'undefined' && !paypalUpgradeRendered) {
                try {
                    paypal.Buttons({
                        createOrder: (data, actions) => {
                            return actions.order.create({
                                purchase_units: [{
                                    amount: { value: '9.99' }
                                }]
                            });
                        },
                        onApprove: (data, actions) => {
                            return actions.order.capture().then(details => {
                                showToast('Payment successful! Premium activated. 🎉');
                                user.premium = true;
                                saveUser();
                                updateUsageBar();
                                closeModal();
                            });
                        },
                        onError: (err) => {
                            console.error('PayPal error:', err);
                            showToast('PayPal payment failed. Please try again.', 'error');
                        }
                    }).render('#paypal-upgrade-container');
                    paypalUpgradeRendered = true;
                } catch (e) {
                    console.error('Failed to render PayPal upgrade button:', e);
                    upgradeContainer.innerHTML = '<p style="color: var(--error); font-size: 12px;">PayPal unavailable</p>';
                }
            }
        }

        // Initialize Stripe
        // REPLACE 'pk_test_YOUR_STRIPE_KEY_HERE' with your actual Stripe Publishable Key
        let stripe = null;
        try {
            if (typeof Stripe !== 'undefined') {
                stripe = Stripe('pk_test_YOUR_STRIPE_KEY_HERE');
            }
        } catch (e) {
            console.error('Failed to initialize Stripe:', e);
        }

        function startStripeCheckout() {
            if (!stripe) {
                showToast('Stripe is not configured. Please check your internet connection.', 'error');
                return;
            }
            
            // REPLACE 'price_YOUR_MONTHLY_PRICE_ID' with your actual Stripe Price ID for the subscription
            const priceId = 'price_YOUR_MONTHLY_PRICE_ID';
            
            stripe.redirectToCheckout({
                lineItems: [{ price: priceId, quantity: 1 }],
                mode: 'subscription',
                successUrl: window.location.origin + window.location.pathname + '?session_id={CHECKOUT_SESSION_ID}&success=true',
                cancelUrl: window.location.origin + window.location.pathname + '?canceled=true'
            }).then(result => {
                if (result.error) {
                    showToast(result.error.message, 'error');
                }
            });
        }

        function generateReferralLink() {
            const baseUrl = window.location.origin + window.location.pathname;
            const link = `${baseUrl}?ref=${user.referralCode}`;
            document.getElementById('referralInput').value = link;
            
            document.getElementById('referralStats').innerHTML = `
                <div style="margin-top:10px;padding:10px;background:rgba(99,102,241,0.1);border-radius:6px;">
                    <strong>Your Stats:</strong><br>
                    Referrals: ${user.referrals}<br>
                    Code: ${user.referralCode}
                </div>
            `;
            
            navigator.clipboard.writeText(link).then(() => {
                showToast('Referral link copied!');
            });
        }

        // ============================================
        // YAML FIXING & VALIDATION (FIXED & UPGRADED)
        // ============================================
        async function fixYAML() {
            console.log('fixYAML called');
            
            // Check free limit for non-premium users
            if (!user.premium && user.fixesUsed >= 5) {
                document.getElementById('limitBanner').classList.add('show');
                showToast('Free limit reached! Upgrade to Premium for unlimited fixes.', 'warning');
                return;
            }
            
            // Get DOM elements
            const inputEl = document.getElementById('input');
            const outputEl = document.getElementById('output');
            const highlightEl = document.getElementById('outputHighlight');
            
            if (!inputEl) {
                console.error('Missing input element');
                showToast('Internal error: input missing', 'error');
                return;
            }
            if (!outputEl) {
                console.error('Missing output element');
                showToast('Internal error: output missing', 'error');
                return;
            }
            
            const yaml = inputEl.value || '';
            if (!yaml.trim()) {
                showToast('Please paste some YAML first!', 'error');
                return;
            }

            // Ensure jsyaml exists
            if (!checkJsYamlLoaded()) {
                console.error('jsyaml not found');
                // Try waiting for it
                try {
                    await waitForJsYaml();
                } catch (e) {
                    showToast('Internal error: YAML parser not loaded. Please refresh the page.', 'error');
                    return;
                }
            }

            let parsed = null;
            try {
                parsed = jsyaml.load(yaml);
            } catch (e) {
                console.error('YAML parse error', e);
                showToast('Invalid YAML: ' + e.message, 'error');
                return;
            }

            try {
                // Safe defaults
                if (!parsed.name) parsed.name = 'CI';
                if (!parsed.permissions) parsed.permissions = { contents: 'read' };
                if (!parsed.concurrency) parsed.concurrency = { group: '${{ github.workflow }}-${{ github.ref }}', 'cancel-in-progress': true };

                // Track actions upgraded in this fix
                let thisFixUpgrades = 0;

                function upgradeSteps(steps) {
                    if (!steps) return;
                    steps.forEach(step => {
                        try {
                            if (step && step.uses) {
                                // Regex to extract repo from "owner/repo@version"
                                const match = step.uses.match(/^([^@\/]+\/[^@\/]+)@.+$/);
                                if (match) {
                                    const repo = match[1];
                                    const action = popularActions.find(a => a.repo === repo);
                                    if (action) {
                                        const newUses = `${repo}@${action.current}`;
                                        if (step.uses !== newUses) {
                                            step.uses = newUses;
                                            thisFixUpgrades++;
                                            user.actionsUpgraded = (user.actionsUpgraded || 0) + 1;
                                        }
                                    }
                                }
                            }
                        } catch (inner) {
                            console.warn('Error upgrading step', inner);
                        }
                    });
                }

                Object.values(parsed.jobs || {}).forEach(job => {
                    try { upgradeSteps(job.steps); } catch (e) { console.warn('Error upgrading job steps', e); }
                });

                const fixed = jsyaml.dump(parsed, { indent: 2, lineWidth: -1, noRefs: true });

                // Write output safely
                try {
                    outputEl.value = fixed;
                    if (highlightEl) highlightEl.innerHTML = syntaxHighlight(fixed);
                    try { updateLineNumbers('output'); } catch (e) { /* non-critical */ }
                } catch (domErr) {
                    console.error('Failed to write output to DOM', domErr);
                    showToast('Internal error writing output', 'error');
                    return;
                }

                // Book-keeping
                user.fixesUsed = (user.fixesUsed || 0) + 1;
                user.totalFixes = (user.totalFixes || 0) + 1;
                user.sessions = user.sessions || [];
                user.sessions.push({
                    date: new Date().toISOString(),
                    fixes: user.fixesUsed,
                    actionsUpgraded: thisFixUpgrades
                });
                saveUser();

                updateUsageBar();
                updateStats();
                runQuickValidation();
                renderGraph();
                updateAnalytics();

                // Show banner if free limit reached
                if (!user.premium && user.fixesUsed >= 5) {
                    const limitBanner = document.getElementById('limitBanner');
                    if (limitBanner) limitBanner.classList.add('show');
                    showToast(`Fixed! ${thisFixUpgrades} actions upgraded. You have used all free fixes.`, 'warning');
                } else {
                    showToast(`Fixed! ${thisFixUpgrades} actions upgraded.`);
                }
            } catch (e) {
                console.error('Unexpected error in fixYAML', e);
                showToast('Unexpected error while fixing YAML', 'error');
            }
        }

        function syntaxHighlight(text) {
            if (!text) return '';
            let html = text
                .replace(/&/g, '&amp;')
                .replace(/</g, '&lt;')
                .replace(/>/g, '&gt;');
            
            html = html.replace(/#.*$/gm, '<span class="yaml-comment">$&</span>');
            html = html.replace(/^(\s*)([\w-]+):/gm, '$1<span class="yaml-key">$2</span>:');
            html = html.replace(/:\s*(['"])(.*?)\1/g, ': <span class="yaml-string">$2</span>');
            html = html.replace(/(uses:\s*)([\w-\/]+@[\w\.\-]+)/g, '$1<span class="yaml-action">$2</span>');
            html = html.replace(/\b(\d+)\b/g, '<span class="yaml-number">$1</span>');
            
            return html;
        }

        function handleInput() {
            const input = document.getElementById('input');
            document.getElementById('inputHighlight').innerHTML = syntaxHighlight(input.value);
            updateLineNumbers('input');
            
            // Try to parse but don't show error if js-yaml not loaded yet
            if (checkJsYamlLoaded()) {
                try {
                    parsedWorkflow = jsyaml.load(input.value);
                    updateStats();
                    runQuickValidation();
                } catch (e) {
                    parsedWorkflow = null;
                    const container = document.getElementById('quickValidation');
                    container.innerHTML = `<div class="validation-item error">YAML syntax error: ${e.message}</div>`;
                    document.getElementById('statIssues').textContent = '1';
                    document.getElementById('statIssues').style.color = 'var(--error)';
                }
            }
            
            updateCursorPosition();
        }

        function syncScroll() {
            document.getElementById('inputHighlight').scrollTop = document.getElementById('input').scrollTop;
        }

        function updateLineNumbers(id) {
            const ta = document.getElementById(id);
            if (!ta) return;
            const ln = document.getElementById(id === 'input' ? 'inputLineNumbers' : 'outputLineNumbers');
            if (!ln) return;
            const lines = ta.value.split('\n').length;
            ln.innerHTML = Array.from({length: Math.max(lines, 5)}, (_, i) => `<span>${i+1}</span>`).join('');
        }

        function updateCursorPosition() {
            const ta = document.getElementById('input');
            const pos = ta.selectionStart;
            const text = ta.value.substring(0, pos);
            const lines = text.split('\n');
            const col = lines[lines.length - 1].length + 1;
            document.getElementById('cursorPosition').textContent = `Ln ${lines.length}, Col ${col}`;
        }

        // ============================================
        // STATS & VALIDATION
        // ============================================
        function updateUsageBar() {
            const limit = user.premium ? Infinity : 5;
            const used = user.fixesUsed;
            const percent = limit === Infinity ? 100 : Math.min((used / limit) * 100, 100);
            document.getElementById('usageFill').style.width = percent + '%';
            document.getElementById('usageFill').style.background = user.premium ? 'linear-gradient(135deg, #10b981, #059669)' : 'var(--accent-primary)';
            document.getElementById('usageText').textContent = user.premium ? `✨ Premium: ${used}/${limit} Unlimited fixes` : `${used}/5 free fixes used`;
            
            if (!user.premium && user.fixesUsed >= 5) {
                document.getElementById('limitBanner').classList.add('show');
            } else {
                document.getElementById('limitBanner').classList.remove('show');
            }
        }

        function updateStats() {
            if (!parsedWorkflow || !parsedWorkflow.jobs) {
                document.getElementById('statJobs').textContent = '0';
                document.getElementById('statSteps').textContent = '0';
                document.getElementById('statActions').textContent = '0';
                return;
            }
            
            const jobs = parsedWorkflow.jobs;
            const jobCount = Object.keys(jobs).length;
            const stepCount = Object.values(jobs).reduce((acc, job) => acc + (job.steps || []).length, 0);
            let actionCount = 0;
            
            Object.values(jobs).forEach(job => {
                (job.steps || []).forEach(step => {
                    if (step.uses) actionCount++;
                });
            });
            
            document.getElementById('statJobs').textContent = jobCount;
            document.getElementById('statSteps').textContent = stepCount;
            document.getElementById('statActions').textContent = actionCount;
        }

        function runQuickValidation() {
            const container = document.getElementById('quickValidation');
            container.innerHTML = '';
            
            if (!parsedWorkflow) {
                container.innerHTML = '<div class="validation-item info">Paste YAML to analyze</div>';
                return;
            }
            
            const issues = [];
            if (!parsedWorkflow.on) issues.push({ type: 'error', msg: 'Missing "on:" trigger - workflow will never run' });
            if (!parsedWorkflow.jobs) issues.push({ type: 'error', msg: 'Missing "jobs:" section' });
            if (!parsedWorkflow.permissions) issues.push({ type: 'warning', msg: 'Missing explicit permissions (adds security)' });
            if (!parsedWorkflow.name) issues.push({ type: 'info', msg: 'Consider adding a name to your workflow' });
            
            Object.values(parsedWorkflow.jobs || {}).forEach((job, idx) => {
                if (!job['runs-on'] && !job.strategy?.matrix) {
                    issues.push({ type: 'warning', msg: `Job "${Object.keys(parsedWorkflow.jobs)[idx]}" missing 'runs-on'` });
                }
                if (job.steps?.some(s => s.run && s.run.includes('rm -rf'))) {
                    issues.push({ type: 'warning', msg: `Job "${Object.keys(parsedWorkflow.jobs)[idx]}" contains destructive rm command` });
                }
            });
            
            if (issues.length === 0) issues.push({ type: 'success', msg: '✅ Workflow structure looks good!' });
            
            issues.forEach(issue => {
                const div = document.createElement('div');
                div.className = `validation-item ${issue.type}`;
                div.innerHTML = `<span>${getIssueIcon(issue.type)}</span><span>${issue.msg}</span>`;
                container.appendChild(div);
            });
            
            const errorCount = issues.filter(i => i.type === 'error').length;
            const warningCount = issues.filter(i => i.type === 'warning').length;
            document.getElementById('statIssues').textContent = errorCount + warningCount;
            document.getElementById('statIssues').style.color = errorCount > 0 ? 'var(--error)' : warningCount > 0 ? 'var(--warning)' : 'var(--success)';
        }

        function getIssueIcon(type) {
            switch(type) {
                case 'error': return '❌';
                case 'warning': return '⚠️';
                case 'success': return '✅';
                default: return 'ℹ️';
            }
        }

        function runDeepValidation() {
            const container = document.getElementById('validationContainer');
            
            if (!checkJsYamlLoaded()) {
                container.innerHTML = '<div class="validation-item error">YAML library not loaded yet. Please refresh the page.</div>';
                return;
            }
            
            if (!parsedWorkflow) {
                container.innerHTML = '<div class="validation-item error">No YAML parsed. Please paste and fix YAML in the Editor tab first.</div>';
                return;
            }

            const results = [];
            const jobs = parsedWorkflow.jobs;
            
            if (!parsedWorkflow.permissions) {
                results.push({ type: 'warning', msg: 'Missing least-privilege permissions for GITHUB_TOKEN. This is a security best practice.' });
            }
            
            if (!parsedWorkflow.concurrency) {
                results.push({ type: 'info', msg: 'Add concurrency to prevent overlapping runs and save resources.' });
            }
            
            Object.entries(jobs).forEach(([name, job]) => {
                if (!job['timeout-minutes']) {
                    results.push({ type: 'info', msg: `Job "${name}" has no timeout-minutes set. Consider adding one to prevent hanging jobs.` });
                }
            });
            
            let branchActions = [];
            Object.values(jobs).forEach(job => {
                (job.steps || []).forEach(step => {
                    if (step.uses) {
                        if (step.uses.includes('@main') || step.uses.includes('@master') || step.uses.includes('@HEAD')) {
                            branchActions.push(step.uses);
                        }
                    }
                });
            });
            
            if (branchActions.length > 0) {
                results.push({ type: 'error', msg: `${branchActions.length} action(s) pinned to branch instead of SHA. This is a security risk!` });
            }
            
            const jobNames = Object.keys(jobs);
            const graph = {};
            const indegree = {};
            jobNames.forEach(name => {
                graph[name] = [];
                indegree[name] = 0;
            });
            
            jobNames.forEach(name => {
                const needs = jobs[name].needs || [];
                (Array.isArray(needs) ? needs : [needs]).forEach(dep => {
                    if (graph[dep] !== undefined) {
                        graph[dep].push(name);
                        indegree[name]++;
                    }
                });
            });
            
            const queue = jobNames.filter(n => indegree[n] === 0);
            const visited = new Set(queue);
            
            while (queue.length) {
                const node = queue.shift();
                graph[node].forEach(child => {
                    indegree[child]--;
                    if (indegree[child] === 0 && !visited.has(child)) {
                        visited.add(child);
                        queue.push(child);
                    }
                });
            }
            
            if (visited.size !== jobNames.length) {
                const remaining = jobNames.filter(n => !visited.has(n));
                results.push({ type: 'error', msg: `Circular dependency detected: ${remaining.join(' -> ')}` });
            }
            
            Object.entries(jobs).forEach(([name, job]) => {
                if (!job.if && !job['runs-on']?.includes('ubuntu')) {
                    results.push({ type: 'info', msg: `Job "${name}" could benefit from conditional execution using 'if:'` });
                }
            });
            
            container.innerHTML = '';
            if (results.length === 0) {
                results.push({ type: 'success', msg: '🎉 No issues found! Your workflow follows best practices.' });
            }
            
            const summary = document.createElement('div');
            summary.className = 'validation-item ' + (results.some(r => r.type === 'error') ? 'error' : results.some(r => r.type === 'warning') ? 'warning' : 'success');
            summary.innerHTML = `<strong>Scan Complete:</strong> ${results.length} issue(s) found.`;
            container.appendChild(summary);
            
            results.forEach(res => {
                const div = document.createElement('div');
                div.className = `validation-item ${res.type}`;
                div.innerHTML = `<span>${getIssueIcon(res.type)}</span><div><strong>${res.type.toUpperCase()}:</strong> ${res.msg}</div>`;
                container.appendChild(div);
            });
        }

        // ============================================
        // GRAPH VISUALIZATION & EXPORT
        // ============================================
        function renderGraph() {
            if (!parsedWorkflow || !parsedWorkflow.jobs) {
                document.getElementById('graphContainer').innerHTML = `
                    <div style="text-align: center; padding: 50px; color: var(--text-secondary);">
                        <p style="font-size: 48px; margin-bottom: 20px;">📊</p>
                        <p>Paste a workflow YAML in the Editor tab and click "Fix" to generate the interactive graph.</p>
                    </div>`;
                return;
            }

            const jobs = parsedWorkflow.jobs;
            const jobNames = Object.keys(jobs);
            const graph = {};
            const indegree = {};
            jobNames.forEach(name => {
                graph[name] = [];
                indegree[name] = 0;
            });
            
            jobNames.forEach(name => {
                const needs = jobs[name].needs || [];
                (Array.isArray(needs) ? needs : [needs]).forEach(dep => {
                    if (graph[dep] !== undefined) {
                        graph[dep].push(name);
                        indegree[name]++;
                    }
                });
            });

            const levels = [];
            let queue = jobNames.filter(n => indegree[n] === 0);
            
            while (queue.length) {
                levels.push([...queue]);
                const next = [];
                queue.forEach(node => {
                    graph[node].forEach(child => {
                        indegree[child]--;
                        if (indegree[child] === 0) next.push(child);
                    });
                });
                queue = next;
            }

            const container = document.getElementById('graphContainer');
            container.innerHTML = '';

            if (levels.length === 0 || levels.every(l => l.length === 0)) {
                container.innerHTML = '<p style="color:var(--error);">Unable to generate graph - check for circular dependencies.</p>';
                return;
            }

            levels.forEach((level, idx) => {
                if (level.length === 0) return;
                
                const levelDiv = document.createElement('div');
                levelDiv.className = 'graph-level';
                
                level.forEach(jobName => {
                    const job = jobs[jobName];
                    const node = document.createElement('div');
                    node.className = 'graph-node';
                    
                    const runsOn = job['runs-on'] || (job.strategy?.matrix ? 'Matrix' : 'ubuntu-latest');
                    const needs = job.needs ? (Array.isArray(job.needs) ? job.needs : [job.needs]).join(', ') : 'None';
                    
                    node.innerHTML = `
                        <div class="title">${jobName}</div>
                        <div class="meta">
                            <div style="margin: 5px 0;">🏃 ${runsOn}</div>
                            <div style="margin: 5px 0;">📝 ${(job.steps || []).length} steps</div>
                            <div style="margin: 5px 0; color: ${needs !== 'None' ? 'var(--accent-primary)' : 'var(--text-secondary)'};">⬇️ Needs: ${needs}</div>
                        </div>
                    `;
                    node.onclick = () => highlightJob(jobName);
                    levelDiv.appendChild(node);
                });

                container.appendChild(levelDiv);

                if (idx < levels.length - 1) {
                    const arrowDiv = document.createElement('div');
                    arrowDiv.innerHTML = '<div class="graph-arrow">↓</div>';
                    container.appendChild(arrowDiv);
                }
            });

            const legend = document.createElement('div');
            legend.style.cssText = 'margin-top:30px;padding:15px;background:rgba(0,0,0,0.3);border-radius:8px;font-size:12px;color:var(--text-secondary);';
            legend.innerHTML = '<strong>Legend:</strong> Nodes represent jobs. Arrows show dependencies (execution order). Click a job to highlight.';
            container.appendChild(legend);
        }

        function highlightJob(jobName) {
            showToast(`Selected job: ${jobName}`);
        }

        function exportGraph() {
            const container = document.getElementById('graphContainer');
            if (!container.querySelector('.graph-level')) {
                showToast('No graph to export!', 'error');
                return;
            }

            html2canvas(container).then(canvas => {
                const link = document.createElement('a');
                link.download = 'workflow-graph.png';
                link.href = canvas.toDataURL();
                link.click();
                showToast('Graph exported as image!');
            }).catch(err => {
                console.error('Export failed:', err);
                showToast('Export failed. Try again.', 'error');
            });
        }

        // ============================================
        // DEPENDENCY GENERATOR
        // ============================================
        function generateDependencies() {
            if (!checkJsYamlLoaded()) {
                document.getElementById('depOutput').textContent = 'YAML library not loaded yet. Please refresh the page.';
                return;
            }
            
            if (!parsedWorkflow || !parsedWorkflow.jobs) {
                document.getElementById('depOutput').textContent = 'No workflow parsed. Fix YAML first.';
                return;
            }

            const type = document.getElementById('depType').value;
            const jobs = parsedWorkflow.jobs;
            let output = '';

            switch (type) {
                case 'python':
                    const pythonDeps = new Set();
                    Object.values(jobs).forEach(job => {
                        (job.steps || []).forEach(step => {
                            if (step.run) {
                                const pipMatch = step.run.match(/pip install(?: -r)? (.+)/);
                                if (pipMatch) {
                                    pipMatch[1].split(' ').forEach(pkg => {
                                        if (pkg && !pkg.startsWith('-')) pythonDeps.add(pkg);
                                    });
                                }
                            }
                        });
                    });
                    output = '# requirements.txt\n' + Array.from(pythonDeps).sort().join('\n');
                    break;
                    
                case 'node':
                    const nodeDeps = {};
                    Object.values(jobs).forEach(job => {
                        (job.steps || []).forEach(step => {
                            if (step.run) {
                                const npmMatch = step.run.match(/npm install (.+)/);
                                if (npmMatch) {
                                    npmMatch[1].split(' ').forEach(pkg => {
                                        if (pkg && !pkg.startsWith('-')) {
                                            nodeDeps[pkg] = '^1.0.0';
                                        }
                                    });
                                }
                            }
                        });
                    });
                    output = JSON.stringify({
                        "name": parsedWorkflow.name || "workflow-project",
                        "version": "1.0.0",
                        "dependencies": nodeDeps
                    }, null, 2);
                    break;
                    
                case 'java':
                    const javaDeps = [];
                    Object.values(jobs).forEach(job => {
                        (job.steps || []).forEach(step => {
                            if (step.uses && step.uses.includes('setup-java')) {
                                javaDeps.push({
                                    groupId: 'org.example',
                                    artifactId: 'example-app',
                                    version: '1.0.0'
                                });
                            }
                        });
                    });
                    output = `<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>${parsedWorkflow.name || 'workflow-project'}</artifactId>
  <version>1.0.0</version>
  
  <dependencies>
    ${javaDeps.map(dep => `
    <dependency>
      <groupId>${dep.groupId}</groupId>
      <artifactId>${dep.artifactId}</artifactId>
      <version>${dep.version}</version>
    </dependency>`).join('')}
  </dependencies>
</project>`;
                    break;
                    
                case 'go':
                    const goModules = new Set();
                    Object.values(jobs).forEach(job => {
                        (job.steps || []).forEach(step => {
                            if (step.run) {
                                const goMatch = step.run.match(/go get (.+)/);
                                if (goMatch) {
                                    goMatch[1].split(' ').forEach(mod => {
                                        if (mod && !mod.startsWith('-')) goModules.add(mod);
                                    });
                                }
                            }
                        });
                    });
                    output = `module github.com/example/${parsedWorkflow.name || 'workflow-project'}

go 1.21

require (
${Array.from(goModules).sort().map(mod => `\t${mod} v1.0.0`).join('\n')}
)`;
                    break;
                    
                case 'rust':
                    const rustDeps = {};
                    Object.values(jobs).forEach(job => {
                        (job.steps || []).forEach(step => {
                            if (step.run) {
                                const cargoMatch = step.run.match(/cargo add (.+)/);
                                if (cargoMatch) {
                                    cargoMatch[1].split(' ').forEach(crate => {
                                        if (crate && !crate.startsWith('-')) {
                                            rustDeps[crate] = "0.1.0";
                                        }
                                    });
                                }
                            }
                        });
                    });
                    output = `[package]
name = "${parsedWorkflow.name || 'workflow-project'}"
version = "0.1.0"
edition = "2021"

[dependencies]
${Object.entries(rustDeps).map(([crate, version]) => `${crate} = "${version}"`).join('\n')}`;
                    break;
            }

            document.getElementById('depOutput').textContent = output || 'No dependencies detected in this workflow.';
        }

        // ============================================
        // TEMPLATES
        // ============================================
        function generateTemplates() {
            const templates = {
                'Node.js CI/CD': {
                    yaml: `name: Node.js CI/CD
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]
permissions:
  contents: read
  pull-requests: write
jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [18.x, 20.x, 22.x]
    
    steps:
      - uses: actions/checkout@v6.0.1
      - uses: actions/setup-node@v6.1.0
        with:
          node-version: \${{ matrix.node-version }}
          cache: 'npm'
      
      - name: Install Dependencies
        run: npm ci
      
      - name: Run Tests
        run: npm test
      
      - name: Upload Coverage
        uses: codecov/codecov-action@v5.5.2
        with:
          files: ./coverage/lcov.info
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6.0.1
      - uses: actions/setup-node@v6.1.0
        with:
          node-version: 20
      
      - name: Install & Lint
        run: |
          npm ci
          npm run lint`,
                    desc: 'Complete Node.js workflow with matrix testing, coverage, and linting',
                    tags: ['Node.js', 'CI/CD', 'Testing']
                },
                'Python + Docker': {
                    yaml: `name: Python Docker Build
on:
  push:
    branches: [main]
    tags:
      - 'v*'
  pull_request:
    branches: [main]
permissions:
  contents: read
  packages: write
env:
  REGISTRY: ghcr.io
  IMAGE_NAME: \${{ github.repository }}
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v6.0.1
      
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3.6.1
      
      - name: Log in to Container Registry
        uses: docker/login-action@v3.6.0
        with:
          registry: \${{ env.REGISTRY }}
          username: \${{ github.actor }}
          password: \${{ secrets.GITHUB_TOKEN }}
      
      - name: Extract Metadata
        id: meta
        uses: docker/metadata-action@v5.6.1
        with:
          images: \${{ env.REGISTRY }}/\${{ env.IMAGE_NAME }}
      
      - name: Build & Push Docker Image
        uses: docker/build-push-action@v6.10.0
        with:
          context: .
          push: true
          tags: \${{ steps.meta.outputs.tags }}
          labels: \${{ steps.meta.outputs.labels }}`,
                    desc: 'Build and push Docker images to GitHub Container Registry',
                    tags: ['Python', 'Docker', 'CI/CD']
                },
                'Multi-Platform Deploy': {
                    yaml: `name: Multi-Platform Deploy
on:
  push:
    branches: [main]
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to deploy'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production
permissions:
  contents: read
  id-token: write
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6.0.1
      
      - name: Run Tests
        run: |
          echo "Running tests..."
          npm test
  deploy:
    runs-on: \${{ matrix.os }}
    needs: test
    strategy:
      fail-fast: false
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        include:
          - os: ubuntu-latest
            cloud: aws
          - os: windows-latest
            cloud: azure
          - os: macos-latest
            cloud: gcp
    
    steps:
      - uses: actions/checkout@v6.0.1
      
      - name: Configure \${{ matrix.cloud }}
        run: echo "Configuring \${{ matrix.cloud }}..."
      
      - name: Deploy to \${{ matrix.cloud }}
        run: echo "Deploying to \${{ matrix.cloud }}..."`,
                    desc: 'Deploy to multiple cloud platforms with matrix strategy',
                    tags: ['Multi-Platform', 'Deploy', 'Cloud']
                },
                'Release Manager': {
                    yaml: `name: Release Manager
on:
  push:
    branches: [main]
    tags:
      - 'v*'
  workflow_dispatch:
permissions:
  contents: write
  packages: write
  pull-requests: write
jobs:
  release:
    runs-on: ubuntu-latest
    outputs:
      release_id: \${{ steps.create_release.outputs.id }}
    
    steps:
      - uses: actions/checkout@v6.0.1
        with:
          fetch-depth: 0
      
      - name: Setup Node.js
        uses: actions/setup-node@v6.1.0
        with:
          node-version: 20
      
      - name: Determine Version
        id: version
        run: |
          VERSION=\${{ github.event.inputs.version || github.ref_name }}
          echo "version=\$VERSION" >> \$GITHUB_OUTPUT
      
      - name: Create Release
        id: create_release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: \${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: v\${{ steps.version.outputs.version }}
          release_name: Release v\${{ steps.version.outputs.version }}
          draft: false
          prerelease: false
      
      - name: Upload Release Asset
        uses: actions/upload-release-asset@v1
        env:
          GITHUB_TOKEN: \${{ secrets.GITHUB_TOKEN }}
        with:
          upload_url: \${{ steps.create_release.outputs.upload_url }}
          asset_path: ./dist/release.zip
          asset_name: release-v\${{ steps.version.outputs.version }}.zip
          asset_content_type: application/zip
      
      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v8.0.0
        with:
          title: 'chore: Bump version to \${{ steps.version.outputs.version }}'
          body: 'Automated version bump'
          base: develop`,
                    desc: 'Automated release workflow with version management',
                    tags: ['Release', 'Automation', 'CI/CD']
                },
                'Security Scanner': {
                    yaml: `name: Security Scan
on:
  push:
    branches: [main, develop]
  pull_request:
    paths:
      - '**/*.py'
      - '**/requirements.txt'
  schedule:
    - cron: '0 0 * * 0'  # Weekly scan
permissions:
  contents: read
  security-events: write
jobs:
  secret-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6.0.1
      
      - name: TruffleHog
        uses: trufflesecurity/trufflehog@main
        with:
          path: ./
          base: main
          extra_args: --debug --only-verified
  dependency-audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6.0.1
      
      - name: Set up Python
        uses: actions/setup-python@v6.1.0
        with:
          python-version: '3.12'
      
      - name: Safety Audit
        run: |
          pip install safety
          safety check -r requirements.txt --json > audit.json
      
      - name: Upload Security Results
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: audit.json`,
                    desc: 'Comprehensive security scanning for secrets and dependencies',
                    tags: ['Security', 'Scanner', 'Audit']
                },
                'AWS Deploy': {
                    yaml: `name: AWS Deployment
on:
  push:
    branches: [main]
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to deploy'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production
permissions:
  contents: read
  id-token: write
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: \${{ github.event.inputs.environment || 'staging' }}
    
    steps:
      - uses: actions/checkout@v6.0.1
      
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v5.1.1
        with:
          role-to-assume: arn:aws:iam::\${{ secrets.AWS_ACCOUNT_ID }}:role/GitHubActionsRole
          aws-region: \${{ secrets.AWS_REGION }}
      
      - name: Deploy to AWS
        run: |
          echo "Deploying to \${{ github.event.inputs.environment || 'staging' }}..."
          aws s3 sync ./dist s3://my-bucket-\${{ github.event.inputs.environment || 'staging' }}
      
      - name: Invalidate CloudFront
        if: \${{ github.event.inputs.environment == 'production' }}
        run: |
          aws cloudfront create-invalidation --distribution-id \${{ secrets.CLOUDFRONT_DIST_ID }} --paths "/*"`,
                    desc: 'Deploy to AWS with OIDC and environment selection',
                    tags: ['AWS', 'Cloud', 'Deploy']
                }
            };
            
            const grid = document.getElementById('templateGrid');
            grid.innerHTML = '';
            
            Object.entries(templates).forEach(([name, data]) => {
                const card = document.createElement('div');
                card.className = 'template-card';
                card.innerHTML = `
                    <div style="display:flex;justify-content:space-between;align-items:start;margin-bottom:10px;">
                        <h4>${name}</h4>
                        <span class="premium-badge-small">Free</span>
                    </div>
                    <p style="font-size:12px;color:var(--text-secondary);margin-bottom:10px;">${data.desc}</p>
                    <div style="display:flex;gap:5px;flex-wrap:wrap;">
                        ${data.tags.map(tag => `<span style="background:rgba(99,102,241,0.2);padding:2px 6px;border-radius:4px;font-size:10px;color:var(--accent-primary);">${tag}</span>`).join('')}
                    </div>
                `;
                card.onclick = () => loadTemplate(data.yaml, name);
                grid.appendChild(card);
            });
        }

        function loadTemplate(yaml, name) {
            document.getElementById('input').value = yaml;
            handleInput();
            switchTab('editor', event);
            showToast(`Loaded template: ${name}`);
            
            if (user.templateUsage) {
                user.templateUsage[name] = (user.templateUsage[name] || 0) + 1;
                saveUser();
            }
        }

        // ============================================
        // ANALYTICS
        // ============================================
        function updateAnalytics() {
            document.getElementById('totalFixes').textContent = user.totalFixes;
            
            if (parsedWorkflow && parsedWorkflow.jobs) {
                const jobsFound = Object.keys(parsedWorkflow.jobs).length;
                document.getElementById('totalJobsFound').textContent = 
                    (parseInt(document.getElementById('totalJobsFound').textContent) || 0) + jobsFound;
            }
            
            document.getElementById('totalActionsUpgraded').textContent = user.actionsUpgraded;
            
            updateActivityChart();
            updateTemplateUsage();
        }

        function updateActivityChart() {
            const chart = document.getElementById('activityChart');
            if (!user.sessions || user.sessions.length === 0) {
                chart.innerHTML = '<p style="font-size:13px;color:var(--text-secondary);">Start using the tool to see activity!</p>';
                return;
            }
            
            const days = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'];
            const today = new Date();
            const activity = [];
            
            for (let i = 6; i >= 0; i--) {
                const d = new Date(today);
                d.setDate(d.getDate() - i);
                const dateStr = d.toISOString().split('T')[0];
                const count = user.sessions.filter(s => s.date.startsWith(dateStr)).length;
                activity.push({ label: days[d.getDay()], count });
            }
            
            const maxCount = Math.max(...activity.map(a => a.count), 1);
            
            chart.innerHTML = activity.map(a => `
                <div style="flex:1;display:flex;flex-direction:column;align-items:center;gap:5px;">
                    <div style="width:100%;height:100px;background:rgba(99,102,241,0.1);border-radius:4px;display:flex;align-items:end;overflow:hidden;">
                        <div style="width:100%;height:${Math.max((a.count / maxCount) * 100, 5)}%;background:linear-gradient(to top, var(--accent-primary), var(--accent-secondary));border-radius:4px;transition:height 0.3s;"></div>
                    </div>
                    <span style="font-size:10px;color:var(--text-secondary);">${a.label}</span>
                </div>
            `).join('');
        }

        function updateTemplateUsage() {
            const container = document.getElementById('templateUsage');
            if (!user.templateUsage || Object.keys(user.templateUsage).length === 0) {
                container.innerHTML = '<p style="font-size:13px;color:var(--text-secondary);">Start using templates to see stats here!</p>';
                return;
            }
            
            const sorted = Object.entries(user.templateUsage).sort((a, b) => b[1] - a[1]);
            
            container.innerHTML = sorted.slice(0, 5).map(([name, count], idx) => `
                <div style="display:flex;justify-content:space-between;align-items:center;padding:8px 0;border-bottom:1px solid var(--border);">
                    <span style="font-size:13px;">${idx + 1}. ${name}</span>
                    <span style="background:var(--accent-primary);padding:2px 8px;border-radius:10px;font-size:11px;">${count} uses</span>
                </div>
            `).join('');
        }

        // ============================================
        // FILE OPERATIONS
        // ============================================
        function clearInput() {
            document.getElementById('input').value = '';
            document.getElementById('output').value = '';
            document.getElementById('inputHighlight').innerHTML = '';
            document.getElementById('outputHighlight').innerHTML = '';
            parsedWorkflow = null;
            handleInput();
            updateLineNumbers('input');
            updateLineNumbers('output');
            showToast('Cleared!');
        }

        function copyOutput() {
            const text = document.getElementById('output').value || document.getElementById('input').value;
            if (!text) {
                showToast('Nothing to copy!', 'error');
                return;
            }
            navigator.clipboard.writeText(text).then(() => {
                showToast('Copied to clipboard!');
            });
        }

        function downloadYAML() {
            const text = document.getElementById('output').value || document.getElementById('input').value;
            if (!text) {
                showToast('No YAML to download!', 'error');
                return;
            }
            const blob = new Blob([text], { type: 'text/yaml' });
            const a = document.createElement('a');
            a.href = URL.createObjectURL(blob);
            a.download = 'workflow.yml';
            a.click();
            URL.revokeObjectURL(a.href);
            showToast('Downloaded!');
        }

        function importFile() {
            document.getElementById('fileInput').click();
        }

        function handleFileUpload(e) {
            const file = e.target.files[0];
            if (!file) return;
            
            const reader = new FileReader();
            reader.onload = ev => {
                document.getElementById('input').value = ev.target.result;
                handleInput();
                showToast(`Loaded: ${file.name}`);
            };
            reader.onerror = () => showToast('Error reading file', 'error');
            reader.readAsText(file);
            e.target.value = '';
        }

        function exportAll() {
            const data = {
                exportDate: new Date().toISOString(),
                input: document.getElementById('input').value,
                output: document.getElementById('output').value,
                stats: {
                    jobs: document.getElementById('statJobs').textContent,
                    steps: document.getElementById('statSteps').textContent,
                    actions: document.getElementById('statActions').textContent,
                    issues: document.getElementById('statIssues').textContent
                },
                userStats: {
                    totalFixes: user.totalFixes,
                    actionsUpgraded: user.actionsUpgraded,
                    premium: user.premium
                },
                parsedWorkflow
            };
            
            const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
            const a = document.createElement('a');
            a.href = URL.createObjectURL(blob);
            a.download = `workflow-export-${new Date().toISOString().split('T')[0]}.json`;
            a.click();
            URL.revokeObjectURL(a.href);
            showToast('Exported!');
        }

        // ============================================
        // MODALS
        // ============================================
        function showModal(id) {
            document.getElementById(id).style.display = 'flex';
            
            // Initialize PayPal buttons when modals open
            if (id === 'donateModal' || id === 'upgradeModal') {
                // Small delay to ensure modal is visible
                setTimeout(initPayPalButtons, 100);
            }
        }

        function closeModal() {
            document.querySelectorAll('.modal').forEach(m => m.style.display = 'none');
        }

        // ============================================
        // TOAST NOTIFICATIONS
        // ============================================
        function showToast(message, type = 'success') {
            const existing = document.querySelector('.toast');
            if (existing) existing.remove();

            const toast = document.createElement('div');
            toast.className = 'toast';
            toast.style.background = type === 'error' ? 'var(--error)' : type === 'info' ? 'var(--accent-primary)' : 'var(--success)';
            toast.textContent = message;
            document.body.appendChild(toast);

            setTimeout(() => toast.remove(), 3000);
        }

        // ============================================
        // TAB NAVIGATION
        // ============================================
        function switchTab(tabName, event) {
            if (event && event.target) {
                document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
                event.target.classList.add('active');
            }
            
            document.querySelectorAll('.tab-content').forEach(c => c.style.display = 'none');
            document.getElementById(tabName + '-tab').style.display = 'block';
            
            if (tabName === 'graph') renderGraph();
            if (tabName === 'validation') runDeepValidation();
            if (tabName === 'templates') generateTemplates();
            if (tabName === 'dependencies') generateDependencies();
            if (tabName === 'analytics') updateAnalytics();
        }

        // ============================================
        // INITIALIZATION
        // ============================================
        document.addEventListener('DOMContentLoaded', async () => {
            updateLineNumbers('input');
            updateLineNumbers('output');
            populatePopularActions();
            generateTemplates();
            updateUsageBar();
            updateAnalytics();
            
            const urlParams = new URLSearchParams(window.location.search);
            const ref = urlParams.get('ref');
            if (ref) {
                console.log('Referral code found:', ref);
            }
            
            // Check for Stripe session success
            const success = urlParams.get('success');
            if (success === 'true') {
                const sessionId = urlParams.get('session_id');
                if (sessionId) {
                    showToast('Payment successful! Premium activated. 🎉');
                    user.premium = true;
                    saveUser();
                    updateUsageBar();
                }
                // Clean URL
                window.history.replaceState({}, document.title, window.location.pathname);
            }
            
            // Wait for js-yaml to load if not already loaded
            try {
                await waitForJsYaml();
            } catch (e) {
                console.error('Failed to load js-yaml library');
                showToast('Warning: YAML library not loaded. Some features may not work.', 'error');
            }
            
            if (navigator.onLine) {
                fetchLatestVersions();
            }
            
            document.addEventListener('keydown', (e) => {
                if ((e.ctrlKey || e.metaKey) && e.key === 's') {
                    e.preventDefault();
                    downloadYAML();
                }
                if ((e.ctrlKey || e.metaKey) && e.key === 'Enter') {
                    e.preventDefault();
                    fixYAML();
                }
            });
        });
        
        // Modal close on outside click
        document.addEventListener('click', function(event) {
            if (event.target.classList.contains('modal')) {
                closeModal();
            }
        });
    </script>

    <!-- ============================================ -->
    <!-- MODALS -->
    <!-- ============================================ -->

    <!-- Donate Modal -->
    <div class="modal" id="donateModal">
        <div class="modal-content">
            <h2>💰 Support Development</h2>
            <p>Love GitHub Workflow YAML Fixer Pro? Help us grow and keep it free for everyone!</p>
            
            <div class="features" style="text-align: left; margin: 20px 0;">
                <h4 style="margin-bottom: 15px; color: var(--accent-primary);">Why Donate?</h4>
                <ul style="list-style: none; padding: 0;">
                    <li style="margin: 10px 0;">✅ Keeps the tool free and accessible</li>
                    <li style="margin: 10px 0;">✅ Funds new features and templates</li>
                    <li style="margin: 10px 0;">✅ Supports server and hosting costs</li>
                    <li style="margin: 10px 0;">✅ Helps maintain browser extensions</li>
                </ul>
            </div>
            
            <div style="margin: 20px 0; text-align: left;">
                <h4 style="margin-bottom: 10px;">PayPal Payment ($5.00):</h4>
                <div id="paypal-donate-container" style="max-width: 300px; margin: 0 auto;"></div>
            </div>
            
            <button class="btn-secondary" onclick="closeModal()" aria-label="Close modal">Close</button>
        </div>
    </div>

    <!-- Upgrade Modal -->
    <div class="modal" id="upgradeModal">
        <div class="modal-content">
            <h2>🚀 Go Premium</h2>
            <p>Unlock unlimited power for your CI/CD workflows!</p>
            
            <div class="price">$9.99<span style="font-size:16px;color:var(--text-secondary);">/month</span></div>
            
            <div class="features">
                <h4 style="margin-bottom: 15px; color: var(--accent-gold);">✨ Premium Features</h4>
                <ul style="list-style: none; padding: 0;">
                    <li style="margin: 8px 0;">✅ <strong>Unlimited</strong> YAML fixes per day</li>
                    <li style="margin: 8px 0;">✅ Advanced security scanning</li>
                    <li style="margin: 8px 0;">✅ Circular dependency detection</li>
                    <li style="margin: 8px 0;">✅ Custom workflow templates</li>
                    <li style="margin: 8px 0;">✅ Priority support</li>
                    <li style="margin: 8px 0;">✅ Early access to new features</li>
                    <li style="margin: 8px 0;">✅ Export graphs as images</li>
                </ul>
            </div>
            
            <div style="margin: 20px 0;">
                <h4 style="margin-bottom: 10px;">PayPal Subscription ($9.99/month):</h4>
                <div id="paypal-upgrade-container" style="max-width: 300px; margin: 0 auto;"></div>
            </div>
            
            <button class="btn-primary" style="width: 100%; padding: 15px; font-size: 16px; margin: 10px 0;" onclick="startStripeCheckout()">
                💳 Pay with Stripe (Card)
            </button>
            
            <p style="font-size: 12px; color: var(--text-secondary);">30-day money-back guarantee • Cancel anytime</p>
            
            <button class="btn-secondary" onclick="closeModal()" aria-label="Close modal">Close</button>
        </div>
    </div>

    <!-- Buy Modal -->
    <div class="modal" id="buyModal">
        <div class="modal-content">
            <h2 style="color: var(--accent-gold);">💎 Acquire GitHub Workflow YAML Fixer Pro</h2>
            <p style="font-size: 24px; color: var(--accent-gold); margin: 20px 0;">$750,000</p>
            <p style="font-size: 14px; color: var(--text-secondary);">One-time payment • Fully negotiable</p>
            
            <div class="features" style="text-align: left; margin: 20px 0;">
                <h4 style="margin-bottom: 15px;">What's Included:</h4>
                <ul style="list-style: none; padding: 0;">
                    <li style="margin: 10px 0;">✅ <strong>Complete Source Code</strong> - All HTML, CSS, JavaScript</li>
                    <li style="margin: 10px 0;">✅ <strong>Domain & Brand</strong> - workflowfixer.com (transfer included)</li>
                    <li style="margin: 10px 0;">✅ <strong>User Base</strong> - All registered users and their data</li>
                    <li style="margin: 10px 0;">✅ <strong>VS Code Extension</strong> - Ready-to-publish</li>
                    <li style="margin: 10px 0;">✅ <strong>JetBrains Plugin</strong> - Production-ready</li>
                    <li style="margin: 10px 0;">✅ <strong>Stripe Integration</strong> - Subscription system ready</li>
                    <li style="margin: 10px 0;">✅ <strong>Monetization Revenue</strong> - All premium subscribers</li>
                    <li style="margin: 10px 0;">✅ <strong>Documentation</strong> - Setup guides, API docs</li>
                    <li style="margin: 10px 0;">✅ <strong>Support</strong> - 30-day handover assistance</li>
                </ul>
            </div>
            
            <div style="background: rgba(99, 102, 241, 0.1); padding: 15px; border-radius: 8px; margin: 20px 0; border: 1px solid rgba(99, 102, 241, 0.3);">
                <h4 style="margin-bottom: 10px;">📊 Valuation Rationale</h4>
                <p style="font-size: 13px; color: var(--text-secondary);">
                    This tool has demonstrated product-market fit with proven monetization. 
                    Comparable developer tools in this niche have sold for $500K-$2M. 
                    The recurring revenue potential from premium subscriptions, combined with 
                    the extension marketplace opportunities, makes this a high-value acquisition.
                </p>
            </div>
            
            <div style="display: flex; gap: 10px; justify-content: center; margin: 20px 0;">
                <button class="buy-now-btn" style="flex: none; padding: 15px 30px; font-size: 16px;" onclick="window.open('mailto:acquire@workflowfixer.com?subject=Acquisition Inquiry - GitHub Workflow YAML Fixer Pro', '_blank')" aria-label="Contact for purchase">
                    📧 Contact for Purchase
                </button>
            </div>
            
            <p style="font-size: 12px; color: var(--text-secondary);">
                Serious inquiries only. Escrow.com protection available.
            </p>
            
            <button class="btn-secondary" onclick="closeModal()" aria-label="Close modal">Close</button>
        </div>
    </div>
</body>
</html>
```

## VS Code Extension - Fixed Code

```typescript
// client/src/extension.ts
import * as path from 'path';
import { workspace, ExtensionContext, commands, window, Range } from 'vscode';
import {
    LanguageClient,
    LanguageClientOptions,
    ServerOptions,
    TransportKind
} from 'vscode-languageclient/node';

let client: LanguageClient;

export function activate(context: ExtensionContext) {
    const serverModule = context.asAbsolutePath(
        path.join('server', 'out', 'server.js')
    );
    const debugOptions = { execArgv: ['--nolazy', '--inspect=6009'] };

    const serverOptions: ServerOptions = {
        run: { module: serverModule, transport: TransportKind.ipc },
        debug: {
            module: serverModule,
            transport: TransportKind.ipc,
            options: debugOptions
        }
    };

    const clientOptions: LanguageClientOptions = {
        documentSelector: [{ scheme: 'file', language: 'yaml' }],
        synchronize: {
            fileEvents: workspace.createFileSystemWatcher('**/.clientrc')
        }
    };

    client = new LanguageClient(
        'workflowYamlFixer',
        'Workflow YAML Fixer',
        serverOptions,
        clientOptions
    );

    context.subscriptions.push(client.start());

    context.subscriptions.push(commands.registerCommand('workflowYamlFixer.fix', async () => {
        const editor = window.activeTextEditor;
        if (editor && editor.document.languageId === 'yaml') {
            const text = editor.document.getText();
            // Send request to server to fix YAML
            const fixed = await client.sendRequest('fixYaml', { text });
            
            // Apply edits
            editor.edit(editBuilder => {
                const fullRange = new Range(
                    editor.document.positionAt(0),
                    editor.document.positionAt(text.length)
                );
                editBuilder.replace(fullRange, fixed);
            });
            
            window.showInformationMessage(`Fixed workflow YAML!`);
        }
    }));
}

export function deactivate(): Thenable<void> | undefined {
    if (!client) {
        return undefined;
    }
    return client.stop();
}
```

```typescript
// server/src/server.ts
import {
    Connection,
    TextDocuments,
    InitializeParams,
    DidChangeConfigurationNotification,
    InitializeResult,
    ProposedFeatures,
    TextDocument
} from 'vscode-languageserver/node';
import { createConnection } from 'vscode-languageserver/node';
import * as jsYaml from 'js-yaml';

// Create connection
const connection = createConnection(ProposedFeatures.all);
const documents: TextDocuments<TextDocument> = new TextDocuments(TextDocument);

let hasConfigurationCapability = false;

// Popular actions for upgrading (mirrored from web version)
const popularActions = [
    { name: 'checkout', repo: 'actions/checkout', current: 'v6.0.1' },
    { name: 'setup-node', repo: 'actions/setup-node', current: 'v6.1.0' },
    { name: 'setup-python', repo: 'actions/setup-python', current: 'v6.1.0' },
    { name: 'setup-java', repo: 'actions/setup-java', current: 'v5.1.0' },
    { name: 'upload-artifact', repo: 'actions/upload-artifact', current: 'v6.0.0' },
    { name: 'download-artifact', repo: 'actions/download-artifact', current: 'v7.0.0' },
    { name: 'cache', repo: 'actions/cache', current: 'v5.0.1' },
    { name: 'docker-login', repo: 'docker/login-action', current: 'v3.6.0' },
    { name: 'aws-credentials', repo: 'aws-actions/configure-aws-credentials', current: 'v5.1.1' },
    { name: 'setup-go', repo: 'actions/setup-go', current: 'v6.1.0' },
    { name: 'github-script', repo: 'actions/github-script', current: 'v8.0.0' },
    { name: 'create-pull-request', repo: 'peter-evans/create-pull-request', current: 'v8.0.0' },
    { name: 'codecov-action', repo: 'codecov/codecov-action', current: 'v5.5.2' },
    { name: 'setup-rust', repo: 'actions-rs/toolchain', current: 'v1.0.6' }
];

connection.onInitialize((params: InitializeParams) => {
    const capabilities = params.capabilities;
    hasConfigurationCapability = !!capabilities.workspace?.configuration;

    const result: InitializeResult = {
        capabilities: {
            executeCommandProvider: {
                commands: ['fixYaml']
            }
        }
    };
    return result;
});

connection.onInitialized(() => {
    if (hasConfigurationCapability) {
        connection.client.register(DidChangeConfigurationNotification.type, undefined);
    }
});

// Handle Fix Request
connection.onRequest('fixYaml', (params: { text: string }) => {
    try {
        if (!params.text || params.text.trim() === '') {
            return params.text;
        }
        
        const parsed = jsYaml.load(params.text);
        if (!parsed) return params.text;

        // Upgrade actions logic
        const jobs = parsed.jobs || {};
        Object.values(jobs).forEach((job: any) => {
            if (job.steps && Array.isArray(job.steps)) {
                job.steps.forEach((step: any) => {
                    if (step.uses) {
                        const match = step.uses.match(/^([^@\/]+\/[^@\/]+)@.+$/);
                        if (match) {
                            const repo = match[1];
                            const action = popularActions.find(a => a.repo === repo);
                            if (action) {
                                step.uses = `${repo}@${action.current}`;
                            }
                        }
                    }
                });
            }
        });

        // Safe defaults
        if (!parsed.permissions) parsed.permissions = { contents: 'read' };
        if (!parsed.concurrency) parsed.concurrency = { group: '${{ github.workflow }}-${{ github.ref }}', 'cancel-in-progress': true };
        
        return jsYaml.dump(parsed, { indent: 2, lineWidth: -1, noRefs: true });
    } catch (e) {
        connection.console.error(`Error fixing YAML: ${e}`);
        return params.text; // Return original on error
    }
});

documents.listen(connection);
connection.listen();
```

## Summary of Fixes Applied

| Issue | Fix Applied |
|-------|-------------|
| PayPal buttons never rendered | Added `initPayPalButtons()` call in `showModal()` with proper container IDs |
| Free limit bypassed | Added early return in `fixYAML()` when limit reached |
| VS Code extension syntax error | Moved imports to top of file in correct order |
| Missing imports in server | Added `ProposedFeatures` and `TextDocument` imports |
| Truncated action list | Extended server `popularActions` to match web version |
| Payment SDK errors | Added try-catch and error handling for Stripe/PayPal |
| Stripe success handling | Added URL parameter check for successful payment |
| GitHub API rate limits | Added User-Agent and Accept headers to fetch calls |

**Before deploying:** Replace `YOUR_PAYPAL_CLIENT_ID`, `pk_test_YOUR_STRIPE_KEY_HERE`, and `price_YOUR_MONTHLY_PRICE_ID` with your actual credentials.
