<!DOCTYPE html>
<html lang="pl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Zagadki</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(180deg, #0f172a, #1e293b);
      color: #f1f5f9;
      margin: 0;
      padding: 0;
      display: flex;
      flex-direction: column;
      min-height: 100vh;
    }
    main {
      flex: 1;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 20px;
    }
    h1 {
      color: #38bdf8;
    }
    .riddle {
      margin: 12px 0;
      padding: 12px;
      background: #1e293b;
      border-radius: 8px;
      width: 100%;
      max-width: 500px;
    }
    input {
      width: 100%;
      padding: 8px;
      margin-top: 6px;
      border: 1px solid #475569;
      border-radius: 6px;
      background: #0f172a;
      color: #f1f5f9;
    }
    button {
      margin-top: 10px;
      padding: 6px 14px;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      background: #2563eb;
      color: white;
      font-weight: bold;
      font-size: 0.9em;
    }
    button.danger {
      background: #dc2626;
    }
    button.ghost {
      background: transparent;
      border: 1px solid #64748b;
    }
    .final {
      display: none;
      margin-top: 20px;
      padding: 20px;
      background: #0f172a;
      border: 2px solid #38bdf8;
      border-radius: 10px;
      font-size: 1.3em;
      color: #38f89c;
    }
    /* Guzik przyklejony do prawego górnego rogu */
    #giveUp {
      position: fixed;
      top: 10px;
      right: 10px;
      z-index: 100;
    }
    .hint {
      margin-top: 6px;
      color: #facc15;
      font-style: italic;
      display: none;
    }
    .result {
      margin-top: 6px;
      font-weight: bold;
    }
    .correct { color: #22c55e; }
    .wrong { color: #ef4444; }
  </style>
</head>
<body>
  <main>
    <h1>Rozwiąż zagadki</h1>
    <div id="riddles"></div>
    <div class="final" id="final"></div>
  </main>

  <button class="danger" id="giveUp">🔑 Pokaż odpowiedź</button>

  <!-- Potwierdzenie rezygnacji -->
  <div id="confirmModal" style="display:none;position:fixed;inset:0;background:linear-gradient(180deg,rgba(2,6,23,0.6),rgba(2,6,23,0.6));backdrop-filter:blur(4px);align-items:center;justify-content:center;z-index:60">
    <div style="background:linear-gradient(180deg,#0f1742,#0b1020);border:1px solid #2a3477;padding:18px;border-radius:12px;max-width:420px;margin:0 20px;text-align:center;box-shadow:0 20px 60px rgba(2,6,23,0.7)">
      <h3 style="margin:0 0 8px">Czy na pewno chcesz zrezygnować?</h3>
      <p style="margin:0 0 14px;color:#cfe1ff99">Jeśli potwierdzisz, kod zostanie od razu wyświetlony. Ta akcja nie zmienia zapisanych odpowiedzi.</p>
      <div style="display:flex;gap:10px;justify-content:center">
        <button id="confirmYes" class="danger">Tak, pokaż kod</button>
        <button id="confirmNo" class="ghost">Nie, anuluj</button>
      </div>
    </div>
  </div>

  <script>
    const FINAL_CODE = "podwójne dno";
    const RIDDLES = [
      { q: "Co świeci w nocy na niebie, a nie jest słońcem?", a: ["księżyc"], hint: "Świeci na nocnym niebie" },
      { q: "Ma zęby, ale nie gryzie. Co to?", a: ["grzebień"], hint: "Używasz go do włosów" },
      { q: "Czego więcej zabierzesz, tym większe zostawiasz ślady?", a: ["czas", "kroki"], hint: "Zostawiasz je idąc" },
      { q: "Biegnie, a nie ma nóg. Co to?", a: ["czas", "rzeka"], hint: "Może płynąć albo przemijać" },
      { q: "Co zawsze przed tobą, a nigdy nie możesz tego dogonić?", a: ["horyzont"], hint: "Widać go daleko, na końcu widoku" }
    ];

    const riddlesDiv = document.getElementById("riddles");
    const finalDiv = document.getElementById("final");

    RIDDLES.forEach((r, i) => {
      const box = document.createElement("div");
      box.className = "riddle";
      box.innerHTML = `
        <strong>Zagadka ${i+1}:</strong> ${r.q}<br>
        <input type='text' id='ans${i}' />
        <div style="margin-top:8px;display:flex;gap:6px;">
          <button onclick="showHint(${i})">Podpowiedź</button>
          <button onclick="checkOne(${i})">Sprawdź</button>
        </div>
        <div id='hint${i}' class='hint'></div>
        <div id='res${i}' class='result'></div>
      `;
      riddlesDiv.appendChild(box);
    });

    function checkAnswers() {
      let correct = true;
      RIDDLES.forEach((r, i) => {
        const val = document.getElementById(`ans${i}`).value.trim().toLowerCase();
        if (!r.a.includes(val)) correct = false;
      });
      if (correct) showFinal();
    }

    function showHint(i) {
      const h = document.getElementById(`hint${i}`);
      h.textContent = RIDDLES[i].hint;
      h.style.display = 'block';
    }

    function checkOne(i) {
      const val = document.getElementById(`ans${i}`).value.trim().toLowerCase();
      const res = document.getElementById(`res${i}`);
      if (RIDDLES[i].a.includes(val)) {
        res.textContent = "✔️ Dobrze!";
        res.className = 'result correct';
      } else {
        res.textContent = "❌ Źle, spróbuj ponownie";
        res.className = 'result wrong';
      }
      checkAnswers();
    }

    function showFinal() {
      finalDiv.style.display = "block";
      finalDiv.textContent = "Twój kod: " + FINAL_CODE;
    }

    // Obsługa przycisku "Pokaż odpowiedź"
    const $giveUp = document.getElementById("giveUp");
    $giveUp.addEventListener('click', () => {
      const modal = document.getElementById('confirmModal');
      modal.style.display = 'flex';
    });

    document.getElementById('confirmYes').addEventListener('click', () => {
      document.getElementById('confirmModal').style.display = 'none';
      showFinal();
    });

    document.getElementById('confirmNo').addEventListener('click', () => {
      document.getElementById('confirmModal').style.display = 'none';
    });
  </script>
</body>
</html>
