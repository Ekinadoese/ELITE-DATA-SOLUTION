# ELITE-DATA-SOLUTION
A Data Analysis Toolkit that analyses both Data and Business Information. Measures 150 Business parameters, identifies trend with colour codes, runs regression and cointegration, etc. 
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Analyst Toolkit</title>
    <link rel="stylesheet" href="styles.css">
    <link rel="stylesheet" href="chat.css">
    <link rel="stylesheet" href="theme.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.3.2/papaparse.min.js"></script>
    <script src="script.js"></script>
    <script src="button-state.js"></script>
    <script type="module" src="loading.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.16.9/xlsx.full.min.js"></script>
    <script type="module">
        import { 
            handleDataCleaning,
            handleAutomatedEDA,
            handleStatisticalAnalysis,
            handlePredictiveModeling,
            handleCustomVisualization
        } from './data-analysis.js';

        // Attach handlers to window for legacy script access
        window.dataHandlers = {
            handleDataCleaning,
            handleAutomatedEDA, 
            handleStatisticalAnalysis,
            handlePredictiveModeling,
            handleCustomVisualization
        };
    </script>
    <script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
    <script type="module" src="business-params.js"></script>
    <script type="module" src="expose-params.js"></script>
    <script type="module" src="business-analysis-core.js"></script>
</head>
<body>
    <div class="container" id="mainContainer">
        <h1 class="welcome-message">Hello analyst, what do you want to do today?</h1>
        <div class="columns">
            <div class="column data-column">
                <button class="tool-button" id="dataBtn">Data Analysis</button>
            </div>
            <div class="column business-column">
                <button class="tool-button" id="businessBtn">Business Analysis</button>
            </div>
        </div>
    </div>

    <div id="dataAnalysisSection" style="display: none;">
        <button id="backBtn" class="tool-button">← Back</button>
        <div class="analysis-container">
            <h2>Data Analysis Tools</h2>
            <div class="upload-section">
                <input type="file" id="fileUpload" accept=".csv,.xlsx" class="upload-input">
                <div class="or-divider">OR</div>
                <textarea id="manualData" placeholder="Paste data here..." class="data-textarea"></textarea>
            </div>
            <div class="analysis-options" id="analysisOptions">
                <div class="select-all">
                    <input type="checkbox" id="selectAll">
                    <label for="selectAll">Select All</label>
                </div>
                <div class="options-grid">
                    <div class="option-item"><input type="checkbox" id="clean"><label for="clean">Clean Data</label></div>
                    <div class="option-item"><input type="checkbox" id="eda"><label for="eda">EDA</label></div>
                    <div class="option-item"><input type="checkbox" id="stats"><label for="stats">Statistical Analysis</label></div>
                    <div class="option-item"><input type="checkbox" id="predictive"><label for="predictive">Predictive Analysis</label></div>
                    <div class="option-item"><input type="checkbox" id="exploratory"><label for="exploratory">Exploratory Analysis</label></div>
                    <div class="option-item"><input type="checkbox" id="custom-viz"><label for="custom-viz">Custom Visualization</label></div>
                    <div class="option-item custom-option">
                        <input type="text" id="customRequest" placeholder="Custom Analysis Request">
                    </div>
                </div>
                <button id="analyzeBtn" class="tool-button">Analyze Selected Options</button>
            </div>
        </div>
        <div id="outputDisplay">
            <div class="report-controls">
                <button id="downloadReport" class="download-btn">
                    Download Report
                </button>
                <button id="shareReport" class="download-btn">
                    Share Results
                </button>
            </div>
        </div>
        <div class="analysis-container" style="margin-top: 2rem;">
            <h2>Run Simulated Python-Style Analysis</h2>
            <button id="simulateAnalysis" class="tool-button">▶ Run Simulated Analysis</button>
            <div id="simulatedResults" class="output-section"></div>
            <button id="simulateDownload" style="display:none;">📥 Download Results</button>
        </div>
    </div>

    <div id="businessAnalysisSection" style="display: none;">
        <button id="businessBackBtn" class="tool-button business-back">← Back</button>
        <div class="analysis-container business-tools">
            <h2>Business Analysis Toolkit 2.0</h2>
            <div class="parameter-controls">
                <button id="openParamModal" class="open-modal-btn">📊 Select Analysis Parameters</button>
                <div class="selected-params-display" id="selectedParams"></div>
            </div>
            <div class="upload-section" style="margin-top: 1rem;">
                <input type="text" id="businessInput" 
                       placeholder="Company name, URL, or paste financial data..."
                       class="data-textarea">
                <button id="runAnalysis" class="tool-button">🚀 Perform Comprehensive Analysis</button>
            </div>
            <div id="businessOutput" class="output-section"></div>
        </div>
    </div>

    <!-- Parameter Selection Modal -->
    <div id="paramModal" class="viz-modal" style="display: none;">
        <div class="viz-controls">
            <h3>Business Analysis Parameters (150 Metrics)</h3>
            <div class="modal-controls">
                <button onclick="selectAllParams(true)" data-testid="select-all-button">Select All</button>
                <button onclick="selectAllParams(false)" data-testid="clear-all-button">Clear All</button>
                <button onclick="toggleSectionCollapse()" data-testid="toggle-sections-button">Toggle Sections</button>
                <button onclick="closeParamModal()" data-testid="apply-selections-button">Apply Selections</button>
            </div>
            <div class="param-grid" id="paramGrid">
                <!-- Categories populated dynamically from analysisCategories -->
            </div>
        </div>
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/pptxgenjs@3.11.0/dist/pptxgen.bundle.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/exceljs/4.4.0/exceljs.min.js"></script>
    <script type="module" src="business-analysis-handlers.js"></script>
    <script type="module" src="lite-business-analysis.js"></script>
    <script type="module" src="simulated-analysis-handler.js"></script>
    <script type="module" src="analysis-button-handler.js"></script>

    <div class="theme-toggle">
        <button class="toggle-btn" onclick="toggleTheme()">
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor">
                <path d="M12 3V5M5.6 5.6L4.2 4.2M3 12H5M5.6 18.4L4.2 19.8M12 21v-2M18.4 18.4l1.4 1.4M21 12h-2M18.4 5.6l1.4-1.4"/>
                <circle cx="12" cy="12" r="4"/>
            </svg>
        </button>
    </div>
</body>
</html>
