import React, { useState, useCallback } from 'react';

// Tailwind CSS is assumed to be available in the environment.

// Helper function for generating random numbers from a normal distribution
// using the Box-Muller transform.
const generateRandomNormal = (mean, stdDev) => {
  let u = 0, v = 0;
  while (u === 0) u = Math.random(); // Converting [0,1) to (0,1)
  while (v === 0) v = Math.random();
  const z = Math.sqrt(-2.0 * Math.log(u)) * Math.cos(2.0 * Math.PI * v);
  return z * stdDev + mean;
};

// Helper function for generating random values from a categorical distribution.
const generateRandomCategorical = (categories, probabilities) => {
  const rand = Math.random();
  let cumulativeProbability = 0;
  for (let i = 0; i < categories.length; i++) {
    cumulativeProbability += probabilities[i];
    if (rand < cumulativeProbability) {
      return categories[i];
    }
  }
  return categories[categories.length - 1]; // Fallback, should not be reached if probabilities sum to 1
};

// Function to parse the INI-like configuration text
const parseConfig = (configText) => {
  const config = {
    RISK_DATA: {},
    DISTRIBUTIONS: {}
  };
  let currentSection = null;

  const lines = configText.split('\n');
  for (const line of lines) {
    const trimmedLine = line.trim();
    if (trimmedLine.startsWith('[') && trimmedLine.endsWith(']')) {
      currentSection = trimmedLine.substring(1, trimmedLine.length - 1);
    } else if (trimmedLine && currentSection) {
      const parts = trimmedLine.split('=');
      if (parts.length === 2) {
        const key = parts[0].trim();
        const value = parts[1].trim();
        if (config[currentSection]) {
          config[currentSection][key] = value;
        }
      }
    }
  }
  return config;
};

// Main App component
const App = () => {
  // State to manage column definitions
  const [columns, setColumns] = useState([]);
  // State to manage the number of rows to generate
  const [numRows, setNumRows] = useState(100);
  // State for displaying messages to the user
  const [message, setMessage] = useState('');
  // State to track if data is being generated (for loading indicator)
  const [isGenerating, setIsGenerating] = useState(false);
  // State for the configuration input text
  const [configInput, setConfigInput] = useState('');
  // State to store parsed distributions config (not used for direct CSV generation, but kept for config loading)
  const [parsedDistributions, setParsedDistributions] = useState({});
  // State for the pseudocode input for Gemini API
  const [pseudocodeInput, setPseudocodeInput] = useState('');
  // State for Gemini API loading
  const [isGeminiLoading, setIsGeminiLoading] = useState(false);
  // NEW: State for user's Gemini API key
  const [userApiKey, setUserApiKey] = useState('');

  // Function to add a new column with default settings
  const addColumn = useCallback(() => {
    setColumns(prevColumns => [
      ...prevColumns,
      {
        id: crypto.randomUUID(), // Unique ID for the column
        name: `Spalte ${prevColumns.length + 1}`,
        type: 'number',
        distribution: 'uniform',
        params: { min: 0, max: 100 },
        expression: '' // Used for JavaScript expressions
      }
    ]);
  }, []);

  // Function to update a specific column's properties
  const updateColumn = useCallback((id, key, value) => {
    setColumns(prevColumns =>
      prevColumns.map(col =>
        col.id === id ? { ...col, [key]: value } : col
      )
    );
  }, []);

  // Function to remove a column
  const removeColumn = useCallback((id) => {
    setColumns(prevColumns => prevColumns.filter(col => col.id !== id));
  }, []);

  // Function to handle changes in distribution parameters (e.g., min/max, mean/stdDev)
  const updateColumnParams = useCallback((id, paramKey, paramValue) => {
    setColumns(prevColumns =>
      prevColumns.map(col =>
        col.id === id
          ? { ...col, params: { ...col.params, [paramKey]: paramValue } }
          : col
      )
    );
  }, []);

  // Function to load configuration from the input text
  const loadConfig = useCallback(() => {
    setMessage('');
    try {
      const parsedConfig = parseConfig(configInput);
      const newColumns = [];
      const distributions = parsedConfig.DISTRIBUTIONS;
      setParsedDistributions(distributions); // Store for potential future use or display

      // Helper to create categorical column
      const createCategoricalColumn = (name, rawValues, type = 'string', probKey = null) => {
        const categories = rawValues.split(',').map(s => s.trim()).filter(s => s !== '');
        let probabilities = [];

        if (probKey && distributions[probKey]) {
          const rawProbs = distributions[probKey].split(',').map(s => parseFloat(s.trim()));
          if (rawProbs.length === categories.length) {
            probabilities = rawProbs;
          } else {
            setMessage(prev => prev + `\nWarnung: Anzahl der Wahrscheinlichkeiten für '${name}' (${rawProbs.length}) stimmt nicht mit der Anzahl der Kategorien (${categories.length}) überein. Verwende gleiche Wahrscheinlichkeiten.`);
            probabilities = Array(categories.length).fill(1 / categories.length);
          }
        } else {
          probabilities = Array(categories.length).fill(1 / categories.length);
        }

        // --- AUTOMATIC NORMALIZATION ADDED HERE ---
        const sumProb = probabilities.reduce((sum, p) => sum + p, 0);
        if (Math.abs(sumProb - 1) > 0.001) { // Allow for floating point inaccuracies
          setMessage(prev => prev + `\nWarnung: Wahrscheinlichkeiten für '${name}' summieren sich nicht zu 1 (Summe: ${sumProb.toFixed(2)}). Normalisiere.`);
          const factor = 1 / sumProb;
          probabilities = probabilities.map(p => p * factor);
        }
        // --- END NORMALIZATION ---

        return {
          id: crypto.randomUUID(),
          name: name,
          type: type,
          distribution: 'categorical',
          params: {
            categories: categories.join(', '),
            probabilities: probabilities.map(p => p.toFixed(2)).join(', ') // Format for display
          }
        };
      };

      // Helper to create fixed value column using JavaScript expression
      const createFixedColumn = (name, value, type) => {
        const jsValue = type === 'string' ? `'${value}'` : value;
        return {
          id: crypto.randomUUID(),
          name: name,
          type: type,
          distribution: 'expression',
          expression: `return ${jsValue}`
        };
      };

      // Iterate through RISK_DATA and create columns
      const riskData = parsedConfig.RISK_DATA;
      for (const key in riskData) {
        const value = riskData[key];

        // Special handling for each key based on its expected type and distribution
        if (key === '_WF_PT_RISK') {
          newColumns.push(createFixedColumn(key, value, 'string'));
        } else if (key === 'STATUSES') {
          newColumns.push(createCategoricalColumn(key, value, 'string'));
        } else if (key === 'BOB_NF_ARNDT') {
          // Special handling for BOB_NF_ARNDT due to potential trailing comma and associated probs
          const categories = value.split(',').map(s => s.trim()).filter(s => s !== '');
          let probabilities = [];
          if (distributions.bob_nf_arndt_probs) {
            const rawProbs = distributions.bob_nf_arndt_probs.split(',').map(s => parseFloat(s.trim()));
            if (rawProbs.length >= categories.length) {
              probabilities = rawProbs.slice(0, categories.length);
            } else {
              setMessage(prev => prev + `\nWarnung: Nicht genug Wahrscheinlichkeiten für '${key}'. Verwende gleiche Wahrscheinlichkeiten.`);
              probabilities = Array(categories.length).fill(1 / categories.length);
            }
          } else {
            probabilities = Array(categories.length).fill(1 / categories.length);
          }
          // Normalize probabilities (even if from config, ensure sum is 1)
          const sumProb = probabilities.reduce((sum, p) => sum + p, 0);
          if (Math.abs(sumProb - 1) > 0.001) {
            const factor = 1 / sumProb;
            probabilities = probabilities.map(p => p * factor);
          }

          newColumns.push({
            id: crypto.randomUUID(),
            name: key,
            type: 'string',
            distribution: 'categorical',
            params: {
              categories: categories.join(', '),
              probabilities: probabilities.map(p => p.toFixed(2)).join(', ')
            }
          });
        }
        else if (key === 'BOB_RISKS_UA_N_DAMAGE_AMOUNT_MAX') {
          newColumns.push(createCategoricalColumn(key, value, 'number', 'impact_distribution'));
        } else if (key === 'BOB_RISKS_UA_N_DAMAGE_AMOUNT_MAX_STRINGS') {
          newColumns.push(createCategoricalColumn(key, value, 'string'));
        } else if (key === '_WF_AA_N_RISK_OCC_PROBA') {
          newColumns.push(createCategoricalColumn(key, value, 'number'));
        } else if (key === '_RISK_RA_RISK_SEV') {
          newColumns.push(createCategoricalColumn(key, value, 'string', 'random_severity'));
        } else if (key === '_WF_AA_N_RISK_OCC_COST') {
          newColumns.push(createFixedColumn(key, parseFloat(value) || 0, 'number'));
        } else if (key === 'BOB_UA_COMMENT_FIELD') {
          newColumns.push(createFixedColumn(key, value, 'string'));
        } else if (key === '_RISK_RA_RISK_CAT') {
          newColumns.push(createCategoricalColumn(key, value, 'string'));
        } else if (key === '_WF_RA_RISK_ACT') {
          newColumns.push(createCategoricalColumn(key, value, 'string'));
        } else if (key === '_ACT_TIME') {
          newColumns.push(createCategoricalColumn(key, value, 'number'));
        } else if (key === '_WF_AA_D_DEALINE_random_days_choice') {
          newColumns.push(createFixedColumn(key, parseFloat(value) || 0, 'number'));
        } else if (key === 'impact_delay_days') {
          newColumns.push(createCategoricalColumn(key, value, 'number', 'delay_weigths'));
        }
      }

      setColumns(newColumns);
      setMessage(prev => (prev ? prev + '\n' : '') + 'Konfiguration erfolgreich geladen.');
    } catch (error) {
      setMessage(`Fehler beim Laden der Konfiguration: ${error.message}`);
      console.error(error);
    }
  }, [configInput]);


  // Function to generate the dummy data
  const generateData = useCallback(() => {
    setMessage('');
    setIsGenerating(true);
    try {
      if (columns.length === 0) {
        setMessage('Bitte fügen Sie mindestens eine Spalte hinzu.');
        setIsGenerating(false);
        return;
      }
      if (numRows <= 0) {
        setMessage('Die Anzahl der Zeilen muss größer als 0 sein.');
        setIsGenerating(false);
        return;
      }

      const data = [];
      for (let i = 0; i < numRows; i++) {
        const row = {};
        // First pass: Generate values for independent distributions (uniform, normal, categorical, date)
        for (const col of columns) {
          if (col.distribution === 'uniform') {
            let value = Math.random() * (col.params.max - col.params.min) + col.params.min;
            if (col.type === 'number') {
              value = parseFloat(value.toFixed(2));
            }
            row[col.name] = value;
          } else if (col.distribution === 'normal') {
            let value = generateRandomNormal(col.params.mean, col.params.stdDev);
            if (col.type === 'number') {
              value = parseFloat(value.toFixed(2));
            }
            row[col.name] = value;
          } else if (col.distribution === 'categorical') {
            try {
              const categories = col.params.categories.split(',').map(s => s.trim());
              const probabilities = col.params.probabilities.split(',').map(s => parseFloat(s.trim()));

              if (categories.length === 0 || probabilities.length === 0 || categories.length !== probabilities.length) {
                throw new Error(`Kategorien oder Wahrscheinlichkeiten für Spalte "${col.name}" sind ungültig.`);
              }
              const sumProbabilities = probabilities.reduce((sum, p) => sum + p, 0);
              let finalProbs = probabilities;
              if (Math.abs(sumProbabilities - 1) > 0.001) { // Allow for floating point inaccuracies
                const factor = 1 / sumProbabilities;
                finalProbs = probabilities.map(p => p * factor);
              }
              let value = generateRandomCategorical(categories, finalProbs);
              if (col.type === 'number') {
                value = parseFloat(value);
              }
              row[col.name] = value;
            } catch (e) {
              setMessage(`Fehler bei der kategorialen Verteilung für Spalte "${col.name}": ${e.message}`);
              setIsGenerating(false);
              return;
            }
          } else if (col.distribution === 'dateRange') {
            try {
              const start = new Date(col.params.startDate).getTime();
              const end = new Date(col.params.endDate).getTime();
              if (isNaN(start) || isNaN(end) || start > end) {
                throw new Error(`Ungültiger Datumsbereich für Spalte "${col.name}".`);
              }
              const randomTime = start + Math.random() * (end - start);
              const date = new Date(randomTime);
              row[col.name] = date.toISOString().split('T')[0]; // Format as YYYY-MM-DD
            } catch (e) {
              setMessage(`Fehler bei der Datumserzeugung für Spalte "${col.name}": ${e.message}`);
              setIsGenerating(false);
              return;
            }
          }
          // Expression columns are skipped in the first pass
        }

        // Second pass: Generate values for expression columns, now that 'row' is populated
        for (const col of columns) {
          if (col.distribution === 'expression') {
            try {
              // Create a function from the expression string, passing 'row' and 'Math' as arguments.
              // This allows expressions like 'row.Age * 1000 + Math.random()'.
              // IMPORTANT: Using eval/Function constructor with user input can be a security risk
              // in a production environment. For a local dummy data generator, it's acceptable.
              const func = new Function('row', 'Math', 'return ' + col.expression);
              row[col.name] = func(row, Math);
            } catch (e) {
              setMessage(`Fehler beim Auswerten des Ausdrucks für Spalte "${col.name}": ${e.message}`);
              setIsGenerating(false);
              return;
            }
          }
        }
        data.push(row);
      }
      downloadCSV(data);
      setMessage(`Erfolgreich ${numRows} Zeilen generiert und als CSV heruntergeladen.`);
    } catch (error) {
      setMessage(`Ein unerwarteter Fehler ist aufgetreten: ${error.message}`);
    } finally {
      setIsGenerating(false);
    }
  }, [columns, numRows]);

  // Function to convert data to CSV format and trigger download
  const downloadCSV = useCallback((data) => {
    if (data.length === 0) return;

    const headers = Object.keys(data[0]);
    const csvRows = [];

    // Add headers
    csvRows.push(headers.join(','));

    // Add data rows
    for (const row of data) {
      const values = headers.map(header => {
        const val = row[header];
        // Handle values that might contain commas or newlines by enclosing them in quotes
        if (typeof val === 'string' && (val.includes(',') || val.includes('\n') || val.includes('"'))) {
          // Escape double quotes by doubling them
          return `"${val.replace(/"/g, '""')}"`;
        }
        return val;
      });
      csvRows.push(values.join(','));
    }

    const csvString = csvRows.join('\n');
    const blob = new Blob([csvString], { type: 'text/csv;charset=utf-8;' });
    const link = document.createElement('a');
    if (link.download !== undefined) { // Feature detection for download attribute
      const url = URL.createObjectURL(blob);
      link.setAttribute('href', url);
      link.setAttribute('download', 'dummy_data.csv');
      link.style.visibility = 'hidden';
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }
  }, []);

  // Function to call Gemini API for syntax help
  const getGeminiSyntaxHelp = useCallback(async (columnId) => {
    setIsGeminiLoading(true);
    setMessage('');
    try {
      const prompt = `You are a JavaScript code generator.
Generate a single JavaScript expression that can be used inside a function like \`(row, Math) => { YOUR_CODE_HERE }\`.
The code must \`return\` a value.
You can access values from other columns using \`row['Column Name']\` (note the bracket notation for column names with spaces).
You can use standard JavaScript \`Math\` functions (e.g., \`Math.random()\`, \`Math.floor()\`).

Generate JavaScript code for the following pseudocode/description:
${pseudocodeInput}
`;

      let chatHistory = [];
      chatHistory.push({ role: "user", parts: [{ text: prompt }] });
      const payload = { contents: chatHistory };
      // Use the user-provided API key if available, otherwise it will be an empty string
      // and the Canvas environment should handle the injection if configured.
      const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${userApiKey}`;

      const response = await fetch(apiUrl, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload)
      });

      const result = await response.json();
      if (result.candidates && result.candidates.length > 0 &&
          result.candidates[0].content && result.candidates[0].content.parts &&
          result.candidates[0].content.parts.length > 0) {
        const generatedCode = result.candidates[0].content.parts[0].text.trim();

        // Clean up common LLM code block formatting
        let cleanedCode = generatedCode;
        if (cleanedCode.startsWith('```javascript')) {
            cleanedCode = cleanedCode.substring('```javascript'.length);
        }
        if (cleanedCode.endsWith('```')) {
            cleanedCode = cleanedCode.substring(0, cleanedCode.length - '```'.length);
        }
        cleanedCode = cleanedCode.trim();

        updateColumn(columnId, 'expression', cleanedCode);
        setMessage('Syntax-Hilfe erfolgreich generiert.');
      } else {
        setMessage('Fehler: Konnte keine Syntax-Hilfe generieren. Bitte versuchen Sie es erneut.');
        console.error('Gemini API response missing content:', result);
      }
    } catch (error) {
      setMessage(`Fehler bei der Gemini API-Anfrage: ${error.message}`);
      console.error('Gemini API error:', error);
    } finally {
      setIsGeminiLoading(false);
    }
  }, [pseudocodeInput, updateColumn, userApiKey]);


  return (
    <div className="min-h-screen bg-gradient-to-b from-blue-700 to-blue-900 flex items-center justify-center p-4 font-['Tahoma','Arial','Verdana',sans-serif]">
      <div className="bg-gray-200 border-2 border-gray-400 rounded-lg shadow-xl w-full max-w-4xl overflow-hidden">
        {/* Windows XP Title Bar */}
        <div className="bg-gradient-to-r from-blue-600 to-blue-800 text-white text-lg font-bold py-1 px-4 flex justify-between items-center rounded-t-lg">
          <span className="select-none">Dummy-Daten-Generator</span>
          <div className="flex space-x-1">
            <button className="w-6 h-6 bg-gray-300 hover:bg-gray-400 text-gray-800 border border-gray-400 rounded-sm flex items-center justify-center text-sm font-bold shadow-sm">_</button>
            <button className="w-6 h-6 bg-gray-300 hover:bg-gray-400 text-gray-800 border border-gray-400 rounded-sm flex items-center justify-center text-sm font-bold shadow-sm">□</button>
            <button className="w-6 h-6 bg-red-500 hover:bg-red-600 text-white border border-red-600 rounded-sm flex items-center justify-center text-sm font-bold shadow-sm">X</button>
          </div>
        </div>

        <div className="p-6 bg-gray-100">
          {/* API Key Input Section */}
          <fieldset className="border-2 border-gray-300 p-4 mb-6 rounded-lg shadow-inner bg-gray-50">
            <legend className="text-gray-700 text-sm font-bold px-2">Gemini API Schlüssel</legend>
            <label htmlFor="api-key-input" className="block text-gray-700 text-sm font-semibold mb-2">
              Ihr Gemini API Schlüssel:
            </label>
            <input
              type="text"
              id="api-key-input"
              value={userApiKey}
              onChange={(e) => setUserApiKey(e.target.value)}
              className="w-full p-2 border border-gray-400 rounded-sm bg-white text-gray-800 focus:outline-none focus:border-blue-500 font-mono text-sm"
              placeholder="Geben Sie Ihren Gemini API Schlüssel hier ein"
            />
            <p className="text-gray-500 text-xs mt-1">
              Dies ist optional. Wenn leer, versucht die Anwendung, den Schlüssel aus der Umgebung zu verwenden.
            </p>
          </fieldset>

          {/* Configuration Input Section */}
          <fieldset className="border-2 border-gray-300 p-4 mb-6 rounded-lg shadow-inner bg-gray-50">
            <legend className="text-gray-700 text-sm font-bold px-2">Konfiguration laden (Optional)</legend>
            <textarea
              id="config-input"
              value={configInput}
              onChange={(e) => setConfigInput(e.target.value)}
              rows="10"
              className="w-full p-2 border border-gray-400 rounded-sm bg-white text-gray-800 focus:outline-none focus:border-blue-500 font-mono text-sm resize-y"
              placeholder="Fügen Sie hier Ihre Konfiguration ein (z.B. aus VS Code)"
            ></textarea>
            <button
              onClick={loadConfig}
              className="w-full mt-4 bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded-md border-2 border-blue-700 shadow-md transform active:scale-98 transition duration-150 ease-in-out"
            >
              Konfiguration laden
            </button>
          </fieldset>

          {/* Number of Rows Input */}
          <fieldset className="border-2 border-gray-300 p-4 mb-6 rounded-lg shadow-inner bg-gray-50">
            <legend className="text-gray-700 text-sm font-bold px-2">Anzahl der Zeilen</legend>
            <label htmlFor="numRows" className="block text-gray-700 text-sm font-semibold mb-2">
              Anzahl der Zeilen:
            </label>
            <input
              type="number"
              id="numRows"
              value={numRows}
              onChange={(e) => setNumRows(parseInt(e.target.value) || 0)}
              min="1"
              className="w-full p-2 border border-gray-400 rounded-sm bg-white text-gray-800 focus:outline-none focus:border-blue-500"
            />
          </fieldset>

          {/* Column Definitions Section */}
          <fieldset className="border-2 border-gray-300 p-4 mb-6 rounded-lg shadow-inner bg-gray-50">
            <legend className="text-gray-700 text-sm font-bold px-2">Spalten definieren</legend>
            {columns.length === 0 ? (
              <p className="text-gray-500 italic mb-4">Noch keine Spalten definiert. Fügen Sie eine hinzu, um zu beginnen!</p>
            ) : (
              <div className="space-y-6">
                {columns.map(col => (
                  <div key={col.id} className="p-4 border-2 border-gray-300 rounded-lg bg-gray-100 shadow-md">
                    <div className="flex justify-between items-center mb-4">
                      <h3 className="text-lg font-bold text-gray-800">
                        Spalte: {col.name}
                      </h3>
                      <button
                        onClick={() => removeColumn(col.id)}
                        className="bg-red-500 hover:bg-red-600 text-white font-bold py-1 px-3 rounded-md border-2 border-red-700 shadow-md transform active:scale-98 transition duration-150 ease-in-out"
                      >
                        Löschen
                      </button>
                    </div>

                    {/* Column Name */}
                    <div className="mb-4">
                      <label htmlFor={`col-name-${col.id}`} className="block text-gray-700 text-sm font-semibold mb-2">
                        Spaltenname:
                      </label>
                      <input
                        type="text"
                        id={`col-name-${col.id}`}
                        value={col.name}
                        onChange={(e) => updateColumn(col.id, 'name', e.target.value)}
                        className="w-full p-2 border border-gray-400 rounded-sm bg-white text-gray-800 focus:outline-none focus:border-blue-500"
                      />
                    </div>

                    {/* Column Type */}
                    <div className="mb-4">
                      <label htmlFor={`col-type-${col.id}`} className="block text-gray-700 text-sm font-semibold mb-2">
                        Datentyp:
                      </label>
                      <select
                        id={`col-type-${col.id}`}
                        value={col.type}
                        onChange={(e) => {
                          updateColumn(col.id, 'type', e.target.value);
                          // Reset distribution and params when type changes
                          if (e.target.value === 'number') {
                            updateColumn(col.id, 'distribution', 'uniform');
                            updateColumn(col.id, 'params', { min: 0, max: 100 });
                            updateColumn(col.id, 'expression', ''); // Reset expression
                          } else if (e.target.value === 'string') {
                            updateColumn(col.id, 'distribution', 'categorical');
                            updateColumn(col.id, 'params', { categories: 'A, B, C', probabilities: '0.33, 0.33, 0.34' });
                            updateColumn(col.id, 'expression', ''); // Reset expression
                          } else if (e.target.value === 'date') {
                            updateColumn(col.id, 'distribution', 'dateRange');
                            updateColumn(col.id, 'params', { startDate: '2000-01-01', endDate: '2025-12-31' });
                            updateColumn(col.id, 'expression', ''); // Reset expression
                          }
                        }}
                        className="w-full p-2 border border-gray-400 rounded-sm bg-white text-gray-800 focus:outline-none focus:border-blue-500"
                      >
                        <option value="number">Zahl</option>
                        <option value="string">Text</option>
                        <option value="date">Datum</option>
                      </select>
                    </div>

                    {/* Column Distribution */}
                    <div className="mb-4">
                      <label htmlFor={`col-dist-${col.id}`} className="block text-gray-700 text-sm font-semibold mb-2">
                        Verteilung:
                      </label>
                      <select
                        id={`col-dist-${col.id}`}
                        value={col.distribution}
                        onChange={(e) => {
                          updateColumn(col.id, 'distribution', e.target.value);
                          // Set default params based on new distribution
                          if (e.target.value === 'uniform') {
                            updateColumn(col.id, 'params', { min: 0, max: 100 });
                            updateColumn(col.id, 'expression', '');
                          } else if (e.target.value === 'normal') {
                            updateColumn(col.id, 'params', { mean: 50, stdDev: 10 });
                            updateColumn(col.id, 'expression', '');
                          } else if (e.target.value === 'categorical') {
                            updateColumn(col.id, 'params', { categories: 'A, B, C', probabilities: '0.33, 0.33, 0.34' });
                            updateColumn(col.id, 'expression', '');
                          } else if (e.target.value === 'dateRange') {
                            updateColumn(col.id, 'params', { startDate: '2000-01-01', endDate: '2025-12-31' });
                            updateColumn(col.id, 'expression', '');
                          } else if (e.target.value === 'expression') {
                            updateColumn(col.id, 'params', null);
                            // Use bracket notation for column names in default expression
                            const firstColName = columns[0]?.name;
                            const defaultExpression = firstColName
                              ? `return row['${firstColName}'] * 10`
                              : `return Math.random() * 100`; // Fallback if no other columns exist
                            updateColumn(col.id, 'expression', defaultExpression);
                          }
                        }}
                        className="w-full p-2 border border-gray-400 rounded-sm bg-white text-gray-800 focus:outline-none focus:border-blue-500"
                      >
                        {col.type === 'number' && (
                          <>
                            <option value="uniform">Gleichverteilung</option>
                            <option value="normal">Normalverteilung</option>
                          </>
                        )}
                        {col.type === 'string' && (
                          <option value="categorical">Kategorial</option>
                        )}
                        {col.type === 'date' && (
                          <option value="dateRange">Datumsbereich</option>
                        )}
                        <option value="expression">Ausdruck (JavaScript)</option>
                      </select>
                    </div>

                    {/* Distribution Parameters */}
                    {col.distribution === 'uniform' && (
                      <div className="flex space-x-4 mb-4">
                        <div className="w-1/2">
                          <label htmlFor={`col-min-${col.id}`} className="block text-gray-700 text-sm font-semibold mb-2">
                            Min:
                          </label>
                          <input
                            type="number"
                            id={`col-min-${col.id}`}
                            value={col.params.min}
                            onChange={(e) => updateColumnParams(col.id, 'min', parseFloat(e.target.value) || 0)}
                            className="w-full p-2 border border-gray-400 rounded-sm bg-white text-gray-800 focus:outline-none focus:border-blue-500"
                          />
                        </div>
                        <div className="w-1/2">
                          <label htmlFor={`col-max-${col.id}`} className="block text-gray-700 text-sm font-semibold mb-2">
                            Max:
                          </label>
                          <input
                            type="number"
                            id={`col-max-${col.id}`}
                            value={col.params.max}
                            onChange={(e) => updateColumnParams(col.id, 'max', parseFloat(e.target.value) || 0)}
                            className="w-full p-2 border border-gray-400 rounded-sm bg-white text-gray-800 focus:outline-none focus:border-blue-500"
                          />
                        </div>
                      </div>
                    )}

                    {col.distribution === 'normal' && (
                      <div className="flex space-x-4 mb-4">
                        <div className="w-1/2">
                          <label htmlFor={`col-mean-${col.id}`} className="block text-gray-700 text-sm font-semibold mb-2">
                            Mittelwert:
                          </label>
                          <input
                            type="number"
                            id={`col-mean-${col.id}`}
                            value={col.params.mean}
                            onChange={(e) => updateColumnParams(col.id, 'mean', parseFloat(e.target.value) || 0)}
                            className="w-full p-2 border border-gray-400 rounded-sm bg-white text-gray-800 focus:outline-none focus:border-blue-500"
                          />
                        </div>
                        <div className="w-1/2">
                          <label htmlFor={`col-stddev-${col.id}`} className="block text-gray-700 text-sm font-semibold mb-2">
                            Standardabweichung:
                          </label>
                          <input
                            type="number"
                            id={`col-stddev-${col.id}`}
                            value={col.params.stdDev}
                            onChange={(e) => updateColumnParams(col.id, 'stdDev', parseFloat(e.target.value) || 0)}
                            className="w-full p-2 border border-gray-400 rounded-sm bg-white text-gray-800 focus:outline-none focus:border-blue-500"
                          />
                        </div>
                      </div>
                    )}

                    {col.distribution === 'categorical' && (
                      <div className="mb-4">
                        <label htmlFor={`col-categories-${col.id}`} className="block text-gray-700 text-sm font-semibold mb-2">
                          Kategorien (Komma-getrennt):
                        </label>
                        <textarea
                          id={`col-categories-${col.id}`}
                          value={col.params.categories}
                          onChange={(e) => updateColumnParams(col.id, 'categories', e.target.value)}
                          rows="2"
                          className="w-full p-2 border border-gray-400 rounded-sm bg-white text-gray-800 focus:outline-none focus:border-blue-500 resize-y"
                          placeholder="z.B. Männlich, Weiblich, Divers"
                        ></textarea>

                        <label htmlFor={`col-probabilities-${col.id}`} className="block text-gray-700 text-sm font-semibold mb-2 mt-4">
                          Wahrscheinlichkeiten (Komma-getrennt, Summe muss 1 ergeben):
                        </label>
                        <textarea
                          id={`col-probabilities-${col.id}`}
                          value={col.params.probabilities}
                          onChange={(e) => updateColumnParams(col.id, 'probabilities', e.target.value)}
                          rows="2"
                          className="w-full p-2 border border-gray-400 rounded-sm bg-white text-gray-800 focus:outline-none focus:border-blue-500 resize-y"
                          placeholder="z.B. 0.49, 0.49, 0.02"
                        ></textarea>
                      </div>
                    )}

                    {col.distribution === 'dateRange' && (
                      <div className="flex space-x-4 mb-4">
                        <div className="w-1/2">
                          <label htmlFor={`col-startDate-${col.id}`} className="block text-gray-700 text-sm font-semibold mb-2">
                            Startdatum:
                          </label>
                          <input
                            type="date"
                            id={`col-startDate-${col.id}`}
                            value={col.params.startDate}
                            onChange={(e) => updateColumnParams(col.id, 'startDate', e.target.value)}
                            className="w-full p-2 border border-gray-400 rounded-sm bg-white text-gray-800 focus:outline-none focus:border-blue-500"
                          />
                        </div>
                        <div className="w-1/2">
                          <label htmlFor={`col-endDate-${col.id}`} className="block text-gray-700 text-sm font-semibold mb-2">
                            Enddatum:
                          </label>
                          <input
                            type="date"
                            id={`col-endDate-${col.id}`}
                            value={col.params.endDate}
                            onChange={(e) => updateColumnParams(col.id, 'endDate', e.target.value)}
                            className="w-full p-2 border border-gray-400 rounded-sm bg-white text-gray-800 focus:outline-none focus:border-blue-500"
                          />
                        </div>
                      </div>
                    )}

                    {col.distribution === 'expression' && (
                      <div className="mb-4">
                        <label htmlFor={`col-expression-${col.id}`} className="block text-gray-700 text-sm font-semibold mb-2">
                          JavaScript-Ausdruck:
                        </label>
                        <textarea
                          id={`col-expression-${col.id}`}
                          value={col.expression}
                          onChange={(e) => updateColumn(col.id, 'expression', e.target.value)}
                          rows="3"
                          className="w-full p-2 border border-gray-400 rounded-sm bg-white text-gray-800 focus:outline-none focus:border-blue-500 font-mono text-sm resize-y"
                          placeholder="z.B. return row['Spalte1'] * 1000 + Math.random() * 100"
                        ></textarea>
                        <p className="text-gray-500 text-xs mt-1">
                          Verwenden Sie `row['Spaltenname']` (mit Anführungszeichen!) um auf andere Spaltenwerte zuzugreifen. `Math` ist ebenfalls verfügbar. Ihr Ausdruck muss einen Wert zurückgeben (z.B. `return ...`).
                        </p>
                        <div className="mt-4 border-t border-gray-300 pt-4">
                          <label htmlFor={`pseudocode-${col.id}`} className="block text-gray-700 text-sm font-semibold mb-2">
                            Pseudocode / Beschreibung (für Syntax-Hilfe):
                          </label>
                          <textarea
                            id={`pseudocode-${col.id}`}
                            value={pseudocodeInput}
                            onChange={(e) => setPseudocodeInput(e.target.value)}
                            rows="2"
                            className="w-full p-2 border border-gray-400 rounded-sm bg-white text-gray-800 focus:outline-none focus:border-blue-500 font-mono text-sm resize-y"
                            placeholder="z.B. alter mal 2 plus eine zufallszahl"
                          ></textarea>
                          <button
                            onClick={() => getGeminiSyntaxHelp(col.id)}
                            disabled={isGeminiLoading}
                            className={`w-full mt-2 ${isGeminiLoading ? 'bg-gray-400 cursor-not-allowed' : 'bg-blue-500 hover:bg-blue-600'} text-white font-bold py-2 px-4 rounded-md border-2 border-blue-700 shadow-md transform active:scale-98 transition duration-150 ease-in-out`}
                          >
                            {isGeminiLoading ? 'Generiere Syntax...' : 'Hilf mir mit Syntax!'}
                          </button>
                        </div>
                      </div>
                    )}
                  </div>
                ))}
              </div>
            )}

            <button
              onClick={addColumn}
              className="w-full mt-6 bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded-md border-2 border-blue-700 shadow-md transform active:scale-98 transition duration-150 ease-in-out"
            >
              Spalte hinzufügen
            </button>
          </fieldset>

          {/* Generate and Download Button */}
          <button
            onClick={generateData}
            disabled={isGenerating}
            className={`w-full ${isGenerating ? 'bg-gray-400 cursor-not-allowed' : 'bg-green-600 hover:bg-green-700'} text-white font-bold py-3 px-4 rounded-md border-2 border-green-700 shadow-md transform active:scale-98 transition duration-150 ease-in-out`}
          >
            {isGenerating ? 'Generiere Daten...' : 'Daten generieren & CSV herunterladen'}
          </button>

          {/* Message Display */}
          {message && (
            <div className={`mt-4 p-3 rounded-lg border-2 ${message.includes('Fehler') || message.includes('Warnung') ? 'bg-red-100 text-red-700 border-red-400' : 'bg-blue-100 text-blue-700 border-blue-400'}`}>
              {message}
            </div>
          )}
        </div>
      </div>
    </div>
  );
};

export default App;
