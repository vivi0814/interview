<template>
    <div class="container">
    <header class="header">
      <h1>CSS 排版調整範例</h1>
    </header>
    <main class="main-content">
      <div class="left-panel">
        <p>左側內容</p>
      </div>
      <div class="right-panel">
        <p>右側內容</p>
      </div>
    </main>
    <footer class="footer">
      <p>版權所有 © 2024</p>
    </footer>
  </div>
</template>

<style lang="scss" scoped>
    .container {
        display: flex;
        flex-direction: column;
        min-height: 100vh;
    }
    .header {
        background-color: #dbc49e;
        color: rgb(95, 65, 5);
        text-align: center;
        padding: 20px;
    }
    .main-content {
        flex: 1;
        display: flex;
        padding: 20px;
        gap: 20px;
    }
    .left-panel,
    .right-panel {
        flex: 1;
        padding: 20px;
        background-color: #f4f4f4;
        border: 1px solid #ccc;
        text-align: center;
    }
    .footer {
        background-color: #333;
        color: white;
        text-align: center;
        padding: 10px;
    }
</style>