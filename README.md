<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Recipe Roulette • Pick What to Cook</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    .line-clamp-3 { display: -webkit-box; -webkit-line-clamp: 3; -webkit-box-orient: vertical; overflow: hidden; }
    .flash { animation: flash 1.1s ease-out; }
    @keyframes flash { 0% { box-shadow: 0 0 0 0 rgba(59,130,246,.6);} 100% { box-shadow: 0 0 0 14px rgba(59,130,246,0);} }
    input:focus, select:focus, textarea:focus { outline: none; box-shadow: 0 0 0 3px rgba(59,130,246,.35); }
  </style>
</head>
<body class="bg-gray-50 text-gray-900 min-h-screen">
  <div class="max-w-7xl mx-auto p-4 sm:p-8">
    <header class="flex flex-col sm:flex-row sm:items-center sm:justify-between gap-4">
      <div>
        <h1 class="text-2xl sm:text-3xl font-extrabold tracking-tight">🍳 Recipe Roulette</h1>
        <p class="text-gray-600">Save recipes, filter, and let chance pick dinner.</p>
      </div>
      <div class="flex flex-wrap gap-2">
        <button id="btnRandom" class="px-4 py-2 rounded-xl bg-indigo-600 text-white font-semibold shadow hover:bg-indigo-500 active:scale-[.98]">🎲 Random from filters</button>
        <button id="btnSurprise" class="px-4 py-2 rounded-xl bg-white border shadow-sm font-semibold hover:bg-gray-50">✨ Surprise me</button>
      </div>
    </header>

    <!-- Filters -->
    <section class="mt-6 grid md:grid-cols-4 gap-3">
      <input id="search" type="text" placeholder="Search name or ingredient…" class="px-3 py-2 rounded-xl bg-white border shadow-sm" />
      <select id="foodTypeSelect" class="px-3 py-2 rounded-xl bg-white border shadow-sm">
        <option value="">Food type</option>
        <option>Vege</option>
        <option>Soup</option>
        <option>Meat</option>
      </select>
      <select id="meatSelect" class="px-3 py-2 rounded-xl bg-white border shadow-sm">
        <option value="">Meat type</option>
        <option>Beef</option>
        <option>Chicken</option>
        <option>Fish</option>
        <option>Pork</option>
        <option>Prawn</option>
        <option>Seafood</option>
      </select>
      <select id="timeSelect" class="px-3 py-2 rounded-xl bg-white border shadow-sm">
        <option value="">Any time</option>
        <option value="15">≤ 15 min</option>
        <option value="30">≤ 30 min</option>
        <option value="45">≤ 45 min</option>
        <option value="60">≤ 60 min</option>
      </select>
    </section>

    <section class="mt-3 grid md:grid-cols-2 gap-3 items-center">
      <input id="include" type="text" placeholder="Must include (comma-separated)…" class="px-3 py-2 rounded-xl bg-white border shadow-sm" />
      <input id="exclude" type="text" placeholder="Exclude (comma-separated)…" class="px-3 py-2 rounded-xl bg-white border shadow-sm" />
    </section>

    <div class="mt-3 flex flex-wrap items-center gap-2">
      <button id="clearFilters" class="px-3 py-2 rounded-xl bg-white border shadow-sm hover:bg-gray-50">Clear filters</button>
      <button id="toggleAdd" class="px-3 py-2 rounded-xl bg-emerald-600 text-white shadow hover:bg-emerald-500">＋ Add recipe</button>
      <button id="exportBtn" class="px-3 py-2 rounded-xl bg-white border shadow-sm hover:bg-gray-50">Export JSON (local backup)</button>
      <label class="px-3 py-2 rounded-xl bg-white border shadow-sm hover:bg-gray-50 cursor-pointer">
        Import JSON
        <input id="importFile" type="file" accept="application/json" class="hidden" />
      </label>
      <span id="status" class="text-sm text-gray-500"></span>
    </div>

    <!-- Add/Edit form -->
    <form id="addForm" class="hidden mt-4 p-4 bg-white rounded-2xl shadow grid sm:grid-cols-2 gap-3">
      <div>
        <label class="text-sm font-medium">Name</label>
        <input id="fName" required type="text" class="w-full mt-1 px-3 py-2 rounded-xl bg-white border shadow-sm" placeholder="e.g., Chickpea Curry" />
      </div>
      <div>
        <label class="text-sm font-medium">Type (Breakfast/Lunch/Dinner)</label>
        <input id="fType" type="text" class="w-full mt-1 px-3 py-2 rounded-xl bg-white border shadow-sm" placeholder="Breakfast, Lunch, Dinner…" />
      </div>
      <div>
        <label class="text-sm font-medium">Food Type</label>
        <select id="fFoodType" class="w-full mt-1 px-3 py-2 rounded-xl bg-white border shadow-sm">
          <option>Vege</option>
          <option>Soup</option>
          <option>Meat</option>
        </select>
      </div>
      <div>
        <label class="text-sm font-medium">Meat Type (optional)</label>
        <select id="fMeatType" class="w-full mt-1 px-3 py-2 rounded-xl bg-white border shadow-sm">
          <option></option>
          <option>Beef</option>
          <option>Chicken</option>
          <option>Fish</option>
          <option>Pork</option>
          <option>Prawn</option>
          <option>Seafood</option>
        </select>
      </div>
      <div>
        <label class="text-sm font-medium">Cuisine (optional)</label>
        <input id="fCuisine" type="text" class="w-full mt-1 px-3 py-2 rounded-xl bg-white border shadow-sm" placeholder="Italian, Chinese…" />
      </div>
      <div>
        <label class="text-sm font-medium">Time (min)</label>
        <input id="fTime" type="number" min="1" class="w-full mt-1 px-3 py-2 rounded-xl bg-white border shadow-sm" placeholder="e.g., 25" />
      </div>
      <div class="sm:col-span-2">
        <label class="text-sm font-medium">Ingredients (comma-separated)</label>
        <textarea id="fIngredients" rows="2" class="w-full mt-1 px-3 py-2 rounded-xl bg-white border shadow-sm" placeholder="onion, garlic, tomato, cumin…"></textarea>
      </div>
      <div>
        <label class="text-sm font-medium">URL (optional)</label>
        <input id="fURL" type="text" class="w-full mt-1 px-3 py-2 rounded-xl bg-white border shadow-sm" placeholder="Link to full recipe" />
      </div>
      <div class="sm:col-span-2 flex justify-between gap-2">
        <button type="button" id="cancelAdd" class="px-4 py-2 rounded-xl bg-white border shadow-sm">Cancel</button>
        <button type="submit" class="px-4 py-2 rounded-xl bg-indigo-600 text-white font-semibold shadow">Save recipe</button>
      </div>
      <p id="editHint" class="sm:col-span-2 text-xs text-amber-600 hidden">You’re editing an existing recipe. Click “Save recipe” to update, or “Cancel” to discard changes.</p>
    </form>

    <p id="resultCount" class="mt-6 text-sm text-gray-600"></p>
    <div id="grid" class="grid sm:grid-cols-2 lg:grid-cols-3 gap-4 mt-2"></div>
  </div>

  <template id="cardTemplate">
    <div class="bg-white rounded-2xl shadow p-4 flex flex-col gap-3 border border-transparent">
      <div class="flex items-start justify-between gap-3">
        <h3 class="title font-semibold text-lg leading-tight"></h3>
        <span class="badge text-xs px-2 py-1 rounded-full bg-gray-100 text-gray-600"></span>
      </div>
      <p class="desc text-sm text-gray-600 line-clamp-3"></p>
      <div class="tags flex flex-wrap gap-2 text-xs"></div>
      <div class="mt-auto flex items-center justify-between">
        <a class="link text-sm underline text-indigo-600 hidden" target="_blank" rel="noopener">Open recipe</a>
        <div class="flex items-center gap-3">
          <button class="edit text-xs text-indigo-600 hover:underline">Edit</button>
          <button class="remove text-xs text-red-600 hover:underline">Remove</button>
        </div>
      </div>
    </div>
  </template>

  <script>
    // ========= SETTINGS =========
    const SETTINGS = {
      MODE: 'sheets', // 'sheets' | 'local' (fallback)
      API_URL: 'https://script.google.com/macros/s/AKfycbxFj_yTPJHwUovMebUpzvXM9EDVa4yDCfuxyTQD_Ht2hmLoAjMBNsYlL5iTNQudrNoO/exec', // <-- your Web App URL
      TOKEN: 'Secretrecipe7' // simple shared-secret to block random writes
    };
    const LS_KEY = 'recipe_roulette_v5_cache';

    // ========= HELPERS =========
    const els = {
      search: document.getElementById('search'),
      foodType: document.getElementById('foodTypeSelect'),
      meatType: document.getElementById('meatSelect'),
      time: document.getElementById('timeSelect'),
      include: document.getElementById('include'),
      exclude: document.getElementById('exclude'),
      clear: document.getElementById('clearFilters'),
      grid: document.getElementById('grid'),
      count: document.getElementById('resultCount'),
      randomBtn: document.getElementById('btnRandom'),
      surpriseBtn: document.getElementById('btnSurprise'),
      status: document.getElementById('status'),
      exportBtn: document.getElementById('exportBtn'),
      importFile: document.getElementById('importFile'),
      toggleAdd: document.getElementById('toggleAdd'),
      addForm: document.getElementById('addForm'),
      cancelAdd: document.getElementById('cancelAdd'),
      fName: document.getElementById('fName'),
      fType: document.getElementById('fType'),
      fFoodType: document.getElementById('fFoodType'),
      fMeatType: document.getElementById('fMeatType'),
      fCuisine: document.getElementById('fCuisine'),
      fTime: document.getElementById('fTime'),
      fURL: document.getElementById('fURL'),
      fIngredients: document.getElementById('fIngredients'),
      cardTpl: document.getElementById('cardTemplate'),
      editHint: document.getElementById('editHint')
    };

    function status(msg, ms = 1300) {
      els.status.textContent = msg; if (!msg) return;
      setTimeout(() => { if (els.status.textContent === msg) els.status.textContent = ''; }, ms);
    }
    const parseCSV = str => (str || '').split(',').map(s => s.trim()).filter(Boolean);

    function getFilters() {
      return {
        q: (els.search.value || '').toLowerCase(),
        foodType: els.foodType.value,
        meatType: els.meatType.value,
        timeMax: Number(els.time.value) || null,
        include: parseCSV(els.include.value.toLowerCase()),
        exclude: parseCSV(els.exclude.value.toLowerCase())
      };
    }

    function matches(recipe, f) {
      const hay = (recipe.name + ' ' + (recipe.ingredients || []).join(' ')).toLowerCase();
      if (f.q && !hay.includes(f.q)) return false;
      if (f.foodType && recipe.foodType !== f.foodType) return false;
      if (f.meatType && recipe.meatType !== f.meatType) return false;
      if (f.timeMax && Number(recipe.time) > f.timeMax) return false;
      const ing = (recipe.ingredients || []).map(x => x.toLowerCase());
      if (f.include.length && !f.include.every(x => ing.includes(x))) return false;
      if (f.exclude.length && f.exclude.some(x => ing.includes(x))) return false;
      return true;
    }

    function render() {
      const f = getFilters();
      const list = state.recipes.filter(r => matches(r, f));
      els.grid.innerHTML = '';
      list.forEach((r) => {
        const card = els.cardTpl.content.firstElementChild.cloneNode(true);
        card.querySelector('.title').textContent = r.name;
        card.querySelector('.badge').textContent = `${r.time || '—'} min · ${r.foodType || '—'}${r.meatType? ' · '+r.meatType:''}`;
        const desc = `Type: ${r.type || '—'}${r.cuisine? ' · Cuisine: '+r.cuisine:''}
Ingredients: ${(r.ingredients||[]).join(', ')}`;
        card.querySelector('.desc').textContent = desc;
        if (r.url) { const link = card.querySelector('.link'); link.classList.remove('hidden'); link.href = r.url; }

        // Remove
        card.querySelector('.remove').addEventListener('click', async () => {
          if (!confirm(`Remove “${r.name}”?`)) return;
          await apiDelete(r.id);
          await refresh();
          status('Removed');
        });

        // Edit → fill the form and switch to "editing" mode
        card.querySelector('.edit').addEventListener('click', () => {
          els.fName.value = r.name || '';
          els.fType.value = r.type || '';
          els.fFoodType.value = r.foodType || '';
          els.fMeatType.value = r.meatType || '';
          els.fCuisine.value = r.cuisine || '';
          els.fTime.value = r.time || '';
          els.fIngredients.value = (r.ingredients || []).join(', ');
          els.fURL.value = r.url || '';
          state.editingId = r.id;
          els.editHint.classList.remove('hidden');
          els.addForm.classList.remove('hidden');
          els.addForm.scrollIntoView({ behavior: 'smooth', block: 'start' });
        });

        els.grid.appendChild(card);
      });
      els.count.textContent = `Showing ${list.length} recipes`;
      return list;
    }

    function randomFrom(list) {
      if (!list.length) { alert('No recipes match your filters. Try clearing some filters.'); return; }
      const idx = Math.floor(Math.random() * list.length);
      const card = els.grid.children[idx];
      if (card) { card.scrollIntoView({ behavior: 'smooth', block: 'center' }); card.classList.add('flash', 'border-indigo-400'); setTimeout(()=> card.classList.remove('flash','border-indigo-400'), 1200); }
    }

    function clearFilters() {
      els.search.value = ''; els.foodType.value = ''; els.meatType.value = ''; els.time.value = ''; els.include.value = ''; els.exclude.value='';
    }

    function csvListToArray(textarea) { return (textarea.value || '').split(',').map(x => x.trim()).filter(Boolean); }

    // ========= SIMPLE API CLIENT (Google Apps Script) =========
    async function apiList() {
      if (SETTINGS.MODE !== 'sheets') return JSON.parse(localStorage.getItem(LS_KEY) || '[]');
      const url = `${SETTINGS.API_URL}?action=list&token=${encodeURIComponent(SETTINGS.TOKEN)}`;
      const res = await fetch(url);
      if (!res.ok) throw new Error('LIST failed');
      const data = await res.json();
      return data.items || [];
    }

    async function apiAdd(item) {
      if (SETTINGS.MODE !== 'sheets') {
        const arr = JSON.parse(localStorage.getItem(LS_KEY) || '[]');
        item.id = Date.now().toString(); arr.push(item); localStorage.setItem(LS_KEY, JSON.stringify(arr));
        return { ok: true };
      }
      const res = await fetch(`${SETTINGS.API_URL}?action=add&token=${encodeURIComponent(SETTINGS.TOKEN)}`, {
        method: 'POST',
        body: JSON.stringify(item)
      });
      if (!res.ok) throw new Error('ADD failed');
      return await res.json();
    }

    // NEW: Update
    async function apiUpdate(item) {
      if (SETTINGS.MODE !== 'sheets') {
        const arr = JSON.parse(localStorage.getItem(LS_KEY) || '[]');
        const i = arr.findIndex(x => x.id === item.id);
        if (i >= 0) arr[i] = item;
        localStorage.setItem(LS_KEY, JSON.stringify(arr));
        return { ok: true };
      }
      const res = await fetch(`${SETTINGS.API_URL}?action=update&token=${encodeURIComponent(SETTINGS.TOKEN)}`, {
        method: 'POST',
        body: JSON.stringify(item)
      });
      if (!res.ok) throw new Error('UPDATE failed');
      return await res.json();
    }

    async function apiDelete(id) {
      if (SETTINGS.MODE !== 'sheets') {
        const arr = JSON.parse(localStorage.getItem(LS_KEY) || '[]').filter(x => x.id !== id);
        localStorage.setItem(LS_KEY, JSON.stringify(arr));
        return { ok: true };
      }
      const res = await fetch(`${SETTINGS.API_URL}?action=delete&token=${encodeURIComponent(SETTINGS.TOKEN)}`, {
        method: 'POST',
        body: JSON.stringify({ id })
      });
      if (!res.ok) throw new Error('DELETE failed');
      return await res.json();
    }

    // ========= STATE & BOOT =========
    const state = { recipes: [], editingId: null };

    async function refresh() {
      try {
        state.recipes = await apiList();
        localStorage.setItem(LS_KEY, JSON.stringify(state.recipes)); // cache for offline
      } catch (e) {
        // fallback to cache
        state.recipes = JSON.parse(localStorage.getItem(LS_KEY) || '[]');
        status('Offline – using local cache');
      }
      render();
    }

    // Add form handlers
    els.toggleAdd.addEventListener('click', () => {
      // If toggling while editing, keep the hint on
      const nowHidden = !els.addForm.classList.contains('hidden');
      els.addForm.classList.toggle('hidden');
      els.editHint.classList.toggle('hidden', state.editingId === null || els.addForm.classList.contains('hidden'));
    });

    els.cancelAdd.addEventListener('click', () => {
      els.addForm.classList.add('hidden');
      els.editHint.classList.add('hidden');
      state.editingId = null;
      els.addForm.reset();
    });

    els.addForm.addEventListener('submit', async (e) => {
      e.preventDefault();
      const obj = {
        id: state.editingId || undefined,
        name: els.fName.value.trim(),
        type: els.fType.value.trim(),
        foodType: els.fFoodType.value,
        meatType: els.fMeatType.value,
        cuisine: els.fCuisine.value.trim(),
        time: Number(els.fTime.value) || null,
        ingredients: csvListToArray(els.fIngredients),
        url: els.fURL.value.trim()
      };
      if (!obj.name) return alert('Please enter a recipe name.');

      if (state.editingId) {
        await apiUpdate({ ...obj, id: state.editingId });
        state.editingId = null;
        status('Updated');
      } else {
        await apiAdd(obj);
        status('Saved');
      }

      await refresh();
      e.target.reset();
      els.addForm.classList.add('hidden');
      els.editHint.classList.add('hidden');
    });

    // Import / export (local backup only)
    els.exportBtn.addEventListener('click', () => {
      const data = JSON.stringify(state.recipes, null, 2);
      const blob = new Blob([data], { type: 'application/json' });
      const a = document.createElement('a'); const ts = new Date().toISOString().slice(0,10);
      a.href = URL.createObjectURL(blob); a.download = `recipes-${ts}.json`; document.body.appendChild(a); a.click(); a.remove();
    });

    els.importFile.addEventListener('change', async (e) => {
      const file = e.target.files?.[0]; if (!file) return; const reader = new FileReader();
      reader.onload = async () => {
        try {
          const arr = JSON.parse(reader.result);
          if (!Array.isArray(arr)) throw new Error('Invalid JSON');
          for (const item of arr) {
            await apiAdd(item);
          }
          await refresh(); status('Imported');
        } catch { alert('Could not import this file. Must be an array of recipes.'); }
        e.target.value = '';
      };
      reader.readAsText(file);
    });

    // Filter events
    ['input','change'].forEach(evt => {
      [els.search, els.foodType, els.meatType, els.time, els.include, els.exclude]
        .forEach(el => el.addEventListener(evt, render));
    });

    els.clear.addEventListener('click', () => { clearFilters(); render(); });
    els.randomBtn.addEventListener('click', () => { const list = render(); randomFrom(list); });
    els.surpriseBtn.addEventListener('click', () => { clearFilters(); const list = render(); randomFrom(list); });

    // Init
    refresh();
  </script>
</body>
</html>
