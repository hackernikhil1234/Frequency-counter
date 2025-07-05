<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Frequency Counter</title>
  <script>
    tailwind = { config: { darkMode: 'class' } }
  </script>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gradient-to-br from-blue-50 to-indigo-100 text-gray-800 transition duration-300 dark:bg-gradient-to-br dark:from-gray-800 dark:to-gray-900 dark:text-gray-200">
  <button id="darkToggle" class="fixed top-4 right-4 bg-gray-800 text-white px-4 py-2 rounded-md z-10">🌙 Dark Mode</button>

  <div class="max-w-3xl mx-auto p-6">
    <h1 class="text-4xl font-bold text-center mb-4">Frequency Counter</h1>
    <p class="text-center text-lg mb-6">Analyze text or array frequency using various modes.</p>

    <!-- Textarea Input -->
    <div id="textInputs">
      <textarea id="inputText" class="w-full p-4 border border-gray-300 rounded-md min-h-[150px] text-gray-800 dark:bg-gray-700 dark:text-white dark:placeholder-gray-300 dark:border-gray-600" placeholder="Enter your text here..."></textarea>
      <button onclick="analyze()" class="mt-4 px-6 py-2 bg-indigo-600 text-white rounded-md hover:bg-indigo-700">Analyze Text</button>
    </div>

    <!-- Array Input -->
    <div id="arrayInputs" class="hidden">
      <input id="arrayInput" type="text" placeholder="Enter array e.g. 1,2,4" class="w-full p-3 border border-gray-300 rounded-md mb-2 text-gray-800 dark:bg-gray-700 dark:text-white dark:placeholder-gray-300 dark:border-gray-600" />
      <input id="kValue" type="number" placeholder="Enter k value" class="w-full p-3 border border-gray-300 rounded-md mb-2 text-gray-800 dark:bg-gray-700 dark:text-white dark:placeholder-gray-300 dark:border-gray-600" />
      <button onclick="analyzeArray()" class="mt-2 px-6 py-2 bg-indigo-600 text-white rounded-md hover:bg-indigo-700">Analyze Array</button>
    </div>

    <!-- Tabs -->
    <div class="flex mt-8 border-b border-gray-300 dark:border-gray-600">
      <div class="tab px-6 py-3 font-bold cursor-pointer border-b-4 border-indigo-600" onclick="switchTab('word')">Word Frequency</div>
      <div class="tab px-6 py-3 font-bold cursor-pointer" onclick="switchTab('char')">Character Frequency</div>
      <div class="tab px-6 py-3 font-bold cursor-pointer" onclick="switchTab('array')">Max Frequency in Array</div>
      <div class="tab px-6 py-3 font-bold cursor-pointer" onclick="switchTab('cpp')">C++ Code</div>
    </div>

    <div id="results" class="mt-6"></div>

    <!-- C++ Code Block (hidden by default, shown on tab) -->
    <div id="cppCodeBlock" class="mt-8 hidden">
      <h2 class="text-2xl font-bold mb-2">C++ Solution for Max Frequency Elements</h2>
      <pre class="bg-gray-100 dark:bg-gray-800 p-4 rounded-md overflow-x-auto text-sm">
<code class="language-cpp">
// C++ code for counting elements with maximum frequency
int maxFrequencyElements(vector<int>& nums) {
    unordered_map<int, int> freq;
    for (int i : nums) freq[i]++;
    int maxFreq = 0, sum = 0;
    for (auto& [num, count] : freq) {
        if (count > maxFreq) {
            maxFreq = count;
            sum = maxFreq;
        } else if (count == maxFreq) {
            sum += maxFreq;
        }
    }
    return sum;
}
</code>
      </pre>
    </div>
  </div>

  <div class="text-center text-sm text-gray-500 dark:text-gray-400 mt-8">
    Frequency Counter &copy; <span id="year"></span>
  </div>

  <script>
    let wordFreq = {};
    let charFreq = {};
    let currentTab = 'word';

    function switchTab(tab) {
      currentTab = tab;

      document.querySelectorAll('.tab').forEach(t => t.classList.remove('border-b-4', 'border-indigo-600'));
      if (tab === 'word') document.querySelectorAll('.tab')[0].classList.add('border-b-4', 'border-indigo-600');
      if (tab === 'char') document.querySelectorAll('.tab')[1].classList.add('border-b-4', 'border-indigo-600');
      if (tab === 'array') document.querySelectorAll('.tab')[2].classList.add('border-b-4', 'border-indigo-600');
      if (tab === 'cpp') document.querySelectorAll('.tab')[3].classList.add('border-b-4', 'border-indigo-600');

      document.getElementById("textInputs").classList.toggle('hidden', tab === 'array' || tab === 'cpp');
      document.getElementById("arrayInputs").classList.toggle('hidden', tab !== 'array');
      document.getElementById("cppCodeBlock").classList.toggle('hidden', tab !== 'cpp');

      if (tab === 'cpp') {
        document.getElementById("results").innerHTML = '';
      } else {
        renderTable(tab);
      }
    }

    function analyze() {
      const text = document.getElementById("inputText").value.toLowerCase();
      wordFreq = {};
      charFreq = {};

      const words = text.match(/\b\w+\b/g) || [];
      words.forEach(word => {
        wordFreq[word] = (wordFreq[word] || 0) + 1;
      });

      const chars = text.replace(/[^a-z]/g, '').split('');
      chars.forEach(char => {
        charFreq[char] = (charFreq[char] || 0) + 1;
      });

      renderTable(currentTab);
    }

    function renderTable(tab) {
      const container = document.getElementById("results");
      if (tab === 'array' || tab === 'cpp') {
        container.innerHTML = '';
        return;
      }

      const freq = tab === 'word' ? wordFreq : charFreq;
      const sorted = Object.entries(freq).sort((a, b) => b[1] - a[1]);
      const maxCount = sorted.length > 0 ? sorted[0][1] : 0;

      if (sorted.length === 0) {
        container.innerHTML = `<p class="text-center text-gray-600 dark:text-gray-400 mt-6">No items found. Please enter text and click "Analyze".</p>`;
        return;
      }

      let html = `<table class="w-full mt-4 table-auto">
        <thead>
          <tr class="text-left border-b border-gray-300 dark:border-gray-500">
            <th class="p-2">${tab === 'word' ? 'Word' : 'Character'}</th>
            <th class="p-2">Count</th>
            <th class="p-2">Bar</th>
          </tr>
        </thead>
        <tbody>`;

      sorted.forEach(([item, count]) => {
        html += `
          <tr class="border-b border-gray-100 dark:border-gray-700">
            <td class="p-2">${item}</td>
            <td class="p-2">${count}</td>
            <td class="p-2">
              <div class="w-full h-4 bg-gray-200 dark:bg-gray-700 rounded overflow-hidden">
                <div class="h-full bg-indigo-600" style="width:${(count / maxCount) * 100}%"></div>
              </div>
            </td>
          </tr>`;
      });

      html += '</tbody></table>';
      container.innerHTML = html;
    }

    function analyzeArray() {
      const arrInput = document.getElementById("arrayInput").value;
      const k = parseInt(document.getElementById("kValue").value);

      const nums = arrInput.split(',').map(x => parseInt(x.trim())).filter(x => !isNaN(x));
      if (!nums.length || isNaN(k)) {
        document.getElementById("results").innerHTML = `<p class="text-red-600 mt-4">Please enter a valid array and k value.</p>`;
        return;
      }

      const result = maxFrequency(nums, k);
      document.getElementById("results").innerHTML = `
        <div class="mt-4 text-center text-lg">
          ✅ Max Frequency After Increasing ≤ <strong>${k}</strong> Units = <span class="text-indigo-600 font-semibold">${result}</span>
        </div>
      `;
    }

    function maxFrequency(nums, k) {
      nums.sort((a, b) => a - b);
      let left = 0, total = 0, res = 0;

      for (let right = 0; right < nums.length; right++) {
        total += nums[right];

        while (nums[right] * (right - left + 1) > total + k) {
          total -= nums[left];
          left++;
        }

        res = Math.max(res, right - left + 1);
      }

      return res;
    }

    // Dark mode toggle
    const darkToggle = document.getElementById('darkToggle');
    darkToggle.addEventListener('click', () => {
      document.documentElement.classList.toggle('dark');
      darkToggle.textContent = document.documentElement.classList.contains('dark') ? '☀ Light Mode' : '🌙 Dark Mode';
    });

    document.getElementById('year').textContent = new Date().getFullYear();
  </script>
</body>
</html>
