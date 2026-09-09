<!DOCTYPE html>
<html lang="uk">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-select=none">
  <title>Dominos Моніторинг</title>
  <style>
    body {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
      background-color: #f4f5f7;
      margin: 0;
      padding: 12px;
      display: flex;
      flex-direction: column;
      align-items: center;
      -webkit-user-select: none;
      user-select: none;
    }
    .header {
      font-size: 16px;
      font-weight: 800;
      color: #222;
      margin-bottom: 15px;
      text-align: center;
      text-transform: uppercase;
      letter-spacing: 0.5px;
    }
    .grid {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 10px;
      width: 100%;
      max-width: 440px;
    }
    .card {
      background-color: white;
      border-radius: 16px;
      padding: 12px;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      box-shadow: 0 4px 10px rgba(0,0,0,0.03);
      border: 2px solid #e4e6eb;
      min-height: 120px;
      box-sizing: border-box;
    }
    .card.stop {
      border-color: #ff3b30;
      background-color: #fff2f1;
    }
    .card.working {
      border-color: #34c759;
      background-color: #f2fbf4;
    }
    .card-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 4px;
    }
    .pizzeria-num {
      font-size: 20px;
      font-weight: 900;
      color: #1c1c1e;
    }
    .timer {
      font-size: 12px;
      font-weight: bold;
      padding: 2px 7px;
      border-radius: 20px;
      background-color: #ff3b30;
      color: white;
      display: none;
    }
    .card.stop .timer {
      display: inline-block;
    }
    .address {
      font-size: 12px;
      color: #8e8e93;
      font-weight: bold;
      margin-bottom: 8px;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }
    .status-line {
      font-size: 13px;
      font-weight: 600;
      color: #2c2c2e;
      margin-bottom: 2px;
    }
    .reason {
      font-size: 11px;
      color: #dd7b00;
      font-weight: bold;
      margin-top: 5px;
      line-height: 1.2;
    }
    .loading {
      font-size: 14px;
      color: #666;
      margin-top: 20px;
    }
  </style>
  <script>
    // Автоматически вытаскиваем ссылку на базу данных из адреса Mini App
    const urlParams = new URLSearchParams(window.location.search);
    const googleScriptUrl = urlParams.get('url');

    async function loadData() {
      if (!googleScriptUrl) {
        document.getElementById('loading').innerText = "❌ Помилка: Не вказано URL бази даних!";
        return;
      }
      try {
        const response = await fetch(googleScriptUrl);
        const data = await response.json();
        renderCards(data);
      } catch (error) {
        console.error("Помилка завантаження даних:", error);
      }
    }
    
    function renderCards(data) {
      const grid = document.getElementById('grid');
      grid.innerHTML = '';
      document.getElementById('loading').style.display = 'none';
      
      data.forEach(function(item) {
        const cardClass = item.status === 'СТОП' ? 'card stop' : 'card working';
        const reasonHtml = item.status === 'СТОП' ? '<div class="reason">⚠️ ' + item.reason + '</div>' : '';
        
        const cardHtml = `
          <div class="${cardClass}">
            <div class="card-header">
              <span class="pizzeria-num">#${item.num}</span>
              <span class="timer">${item.minutes} хв</span>
            </div>
            <div class="address">${item.address}</div>
            <div class="status-line">🛵 Дост: <b>${item.delivery}</b></div>
            <div class="status-line">🥡 Сам: <b>${item.pickup}</b></div>
            ${reasonHtml}
          </div>
        `;
        grid.innerHTML += cardHtml;
      });
    }
    
    // Каждые 15 секунд приложение само обновляет квадратики без перезагрузки страницы
    setInterval(loadData, 15000);
    window.onload = loadData;
  </script>
</head>
<body>
  <div class="header">🍕 Dominos Моніторинг СТОПів</div>
  <div id="loading" class="loading">⏳ Завантаження статусів піцерій...</div>
  <div class="grid" id="grid"></div>
</body>
</html>

