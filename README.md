# ELITE-DATA-SOLUTION
A Data Analysis Toolkit that analyses both Data and Business Information. Measures 150 Business parameters, identifies trend with colour codes, runs regression and cointegration, etc. 
import { performBusinessAnalysis, generateExportContent } from './business-analysis-core.js';
import { showLoading, hideLoading } from './loading.js';
import { getParamName, getSelectedParamCount } from './param-utils.js';

export function initAnalysisButton() {
  const runBtn = document.getElementById('runAnalysis');
  const resultSection = document.getElementById('businessOutput');
  let selectedParams = [];
  
  // Add category count initialization
  if (!window.analysisCategories) {
    window.analysisCategories = [];
  }
  
  // Sync button state with initial selections
  updateButtonState();

  runBtn.addEventListener('click', async () => {
    try {
      validateSelection();
      showLoading(runBtn);
      
      // Perform core analysis
      const analysis = await performBusinessAnalysis(selectedParams);
      renderResults(analysis);
      setupExportHandlers();
      
    } catch (error) {
      handleError(error);
    } finally {
      hideLoading(runBtn);
    }
  });

  // Update on parameter changes
  document.addEventListener('parametersUpdated', () => {
    selectedParams = JSON.parse(localStorage.getItem('selectedMetrics') || '[]');
    updateButtonState();
  });

  // Modified button state update logic
  function updateButtonState() {
    selectedParams = JSON.parse(localStorage.getItem('selectedMetrics') || '[]');
    const count = selectedParams.length;
    
    runBtn.innerHTML = count > 0 
      ? `🚀 Analyze ${count} Selected` 
      : '🚀 Perform Analysis';
    runBtn.disabled = count === 0;
    
    // Update the visible count display
    const countDisplay = document.getElementById('selectedCount');
    if (countDisplay) {
      countDisplay.textContent = `${count} Selected`;
    }
  }

  // Add validation from user's latest code
  function validateSelection() {
    selectedParams = JSON.parse(localStorage.getItem('selectedMetrics') || '[]');
    if (!Array.isArray(selectedParams) || selectedParams.length === 0) {
      throw new Error('Please select at least one analysis category');
    }
  }

  function renderResults(analysis) {
    resultSection.innerHTML = `
      <div class="report-header">
        <h3>${analysis.overview.summary}</h3>
        <div class="report-controls">
          <button class="download-btn" id="downloadBusinessReport">📥 Download</button>
          <button class="download-btn" id="shareBusinessReport">📤 Share</button>
        </div>
      </div>
      <div class="param-grid">
        ${analysis.parameters.map(p => `
          <div class="param-card trend-${p.trend}">
            <div class="param-header">
              <span class="param-name">${getParamName(p.id)}</span>
              <span class="param-score">${p.value}</span>
            </div>
            <div class="param-details">
              <div>Benchmark: ${p.benchmark}</div>
              <div>Trend: ${p.trend}</div>
              <div class="insights">${p.insights}</div>
            </div>
          </div>
        `).join('')}
      </div>`;
  }

  function setupExportHandlers() {
    document.getElementById('downloadBusinessReport').addEventListener('click', handleDownload);
    document.getElementById('shareBusinessReport').addEventListener('click', handleShare);
  }

  function handleDownload() {
    const analysis = JSON.parse(localStorage.getItem('lastAnalysis') || '{}');
    const content = generateExportContent(analysis);
    const blob = new Blob([content], { type: 'text/plain' });
    const link = document.createElement('a');
    link.href = URL.createObjectURL(blob);
    link.download = 'business-analysis-report.txt';
    link.click();
  }

  function handleShare() {
    window.open(`https://www.linkedin.com/sharing/share-offsite/?url=${encodeURIComponent(window.location.href)}`, '_blank');
  }

  function handleError(error) {
    console.error('Analysis error:', error);
    resultSection.innerHTML = `
      <div class="error-message">
        <p>❌ Analysis failed:</p>
        <p>${error.message}</p>
      </div>`;
  }
  
  // Add new business plan handler
  document.getElementById('generatePlanBtn').addEventListener('click', async () => {
    const businessData = {
        name: document.getElementById('businessName').value,
        description: document.getElementById('businessDescription').value,
        industry: document.getElementById('businessIndustry').value,
        location: document.getElementById('businessLocation').value
    };
    
    showLoading(this);
    try {
        const plan = await performBusinessAnalysis(null, businessData, 'business_plan');
        renderBusinessPlan(plan);
    } catch (error) {
        handlePlanError(error);
    }
    hideLoading(this);
  });

  function renderBusinessPlan(plan) {
    const output = document.getElementById('businessOutput');
    output.innerHTML = `
        <div class="business-plan">
            ${renderExecutiveSummary(plan.executive_summary)}
            <div class="full-report-toggle">▼ Show Full Analysis Details</div>
            <div class="full-report">${renderFullReport(plan.full_report)}</div>
        </div>
    `;
    
    // Add toggle functionality
    document.querySelector('.full-report-toggle').addEventListener('click', function() {
        this.parentElement.classList.toggle('expanded');
        this.textContent = this.parentElement.classList.contains('expanded') ? 
            '▲ Collapse Details' : '▼ Show Full Analysis Details';
    });
  }

  function renderExecutiveSummary(executiveSummary) {
    return `
      <div class="executive-summary">
        ${executiveSummary}
      </div>
    `;
  }

  function renderFullReport(fullReport) {
    return `
      <div class="full-report-content">
        ${fullReport}
      </div>
    `;
  }

  function handlePlanError(error) {
    console.error('Plan generation error:', error);
    const output = document.getElementById('businessOutput');
    output.innerHTML = `
      <div class="error-message">
        <p>❌ Plan generation failed:</p>
        <p>${error.message}</p>
      </div>`;
  }
}

// Initialize on page load
document.addEventListener('DOMContentLoaded', () => {
  const runBtn = document.getElementById('runAnalysis');
  if (runBtn) initAnalysisButton();
});
