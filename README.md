<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Объявления</title>

  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: Arial, sans-serif;
    }

    body {
      background: #f2f3f5;
      color: #222;
    }

    header {
      background: #ffffff;
      border-bottom: 1px solid #ddd;
      padding: 15px 5%;
      display: flex;
      justify-content: space-between;
      align-items: center;
      position: sticky;
      top: 0;
      z-index: 10;
    }

    .logo {
      font-size: 26px;
      font-weight: bold;
      color: #1683ff;
    }

    .add-button {
      background: #1683ff;
      color: white;
      border: none;
      padding: 12px 20px;
      border-radius: 8px;
      cursor: pointer;
      font-size: 15px;
    }

    .add-button:hover {
      background: #006fe6;
    }

    .container {
      width: 90%;
      max-width: 1200px;
      margin: 30px auto;
    }

    .search {
      width: 100%;
      padding: 15px;
      border: 1px solid #ddd;
      border-radius: 10px;
      margin-bottom: 25px;
      font-size: 16px;
      background: white;
    }

    .products {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(230px, 1fr));
      gap: 20px;
    }

    .card {
      background: white;
      border-radius: 12px;
      overflow: hidden;
      box-shadow: 0 2px 8px rgba(0,0,0,0.08);
      transition: 0.2s;
    }

    .card:hover {
      transform: translateY(-3px);
    }

    .card img {
      width: 100%;
      height: 180px;
      object-fit: cover;
      background: #eee;
    }

    .card-content {
      padding: 15px;
    }

    .title {
      font-size: 18px;
      font-weight: bold;
      margin-bottom: 8px;
    }

    .price {
      font-size: 21px;
      font-weight: bold;
      margin-bottom: 10px;
      color: #111;
    }

    .description {
      color: #666;
      font-size: 14px;
      line-height: 1.4;
    }

    .delete {
      margin-top: 12px;
      width: 100%;
      padding: 9px;
      border: none;
      border-radius: 7px;
      background: #ffe5e5;
      color: #d60000;
      cursor: pointer;
    }

    .modal {
      display: none;
      position: fixed;
      inset: 0;
      background: rgba(0,0,0,0.5);
      justify-content: center;
      align-items: center;
      padding: 20px;
      z-index: 100;
    }

    .modal.active {
      display: flex;
    }

    .modal-content {
      background: white;
      width: 100%;
      max-width: 500px;
      padding: 25px;
      border-radius: 14px;
    }

    .modal-content h2 {
      margin-bottom: 20px;
    }

    .form-group {
      margin-bottom: 15px;
    }

    .form-group label {
      display: block;
      margin-bottom: 6px;
      font-weight: bold;
    }

    .form-group input,
    .form-group textarea {
      width: 100%;
      padding: 12px;
      border: 1px solid #ccc;
      border-radius: 8px;
      font-size: 15px;
    }

    .form-group textarea {
      resize: vertical;
      min-height: 100px;
    }

    .buttons {
      display: flex;
      gap: 10px;
    }

    .buttons button {
      flex: 1;
      padding: 12px;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      font-size: 15px;
    }

    .save {
      background: #1683ff;
      color: white;
    }

    .cancel {
      background: #eee;
    }

    .empty {
      text-align: center;
      color: #777;
      padding: 50px 0;
    }
  </style>
</head>

<body>

<header>
  <div class="logo">Мои объявления</div>
  <button class="add-button" onclick="openModal()">
    + Подать объявление
  </button>
</header>

<div class="container">

  <input
    type="text"
    id="search"
    class="search"
    placeholder="Поиск объявлений..."
    oninput="renderProducts()"
  >

  <div id="products" class="products"></div>

</div>

<!-- Модальное окно -->
<div class="modal" id="modal">

  <div class="modal-content">

    <h2>Новое объявление</h2>

    <div class="form-group">
      <label>Название</label>
      <input
        type="text"
        id="title"
        placeholder="Например: iPhone 15"
      >
    </div>

    <div class="form-group">
      <label>Цена (€)</label>
      <input
        type="number"
        id="price"
        placeholder="500"
      >
    </div>

    <div class="form-group">
      <label>Описание</label>
      <textarea
        id="description"
        placeholder="Расскажите о товаре..."
      ></textarea>
    </div>

    <div class="form-group">
      <label>Фото</label>
      <input
        type="file"
        id="image"
        accept="image/*"
      >
    </div>

    <div class="buttons">
      <button class="cancel" onclick="closeModal()">
        Отмена
      </button>

      <button class="save" onclick="addProduct()">
        Опубликовать
      </button>
    </div>

  </div>

</div>

<script>

  let products = JSON.parse(
    localStorage.getItem("products")
  ) || [];

  function openModal() {
    document.getElementById("modal").classList.add("active");
  }

  function closeModal() {
    document.getElementById("modal").classList.remove("active");

    document.getElementById("title").value = "";
    document.getElementById("price").value = "";
    document.getElementById("description").value = "";
    document.getElementById("image").value = "";
  }

  function addProduct() {

    const title =
      document.getElementById("title").value.trim();

    const price =
      document.getElementById("price").value;

    const description =
      document.getElementById("description").value.trim();

    const imageInput =
      document.getElementById("image");

    if (!title || !price) {
      alert("Заполните название и цену");
      return;
    }

    const file = imageInput.files[0];

    if (file) {

      const reader = new FileReader();

      reader.onload = function(e) {

        createProduct(
          title,
          price,
          description,
          e.target.result
        );

      };

      reader.readAsDataURL(file);

    } else {

      createProduct(
        title,
        price,
        description,
        "https://via.placeholder.com/500x350?text=Нет+фото"
      );

    }
  }

  function createProduct(
    title,
    price,
    description,
    image
  ) {

    const product = {
      id: Date.now(),
      title: title,
      price: price,
      description: description,
      image: image
    };

    products.unshift(product);

    localStorage.setItem(
      "products",
      JSON.stringify(products)
    );

    closeModal();
    renderProducts();
  }

  function deleteProduct(id) {

    products = products.filter(
      product => product.id !== id
    );

    localStorage.setItem(
      "products",
      JSON.stringify(products)
    );

    renderProducts();
  }

  function renderProducts() {

    const container =
      document.getElementById("products");

    const search =
      document.getElementById("search")
        .value
        .toLowerCase();

    const filtered =
      products.filter(product =>
        product.title
          .toLowerCase()
          .includes(search)
      );

    if (filtered.length === 0) {

      container.innerHTML = `
        <div class="empty">
          <h2>Объявлений пока нет</h2>
          <p>Добавьте первое объявление</p>
        </div>
      `;

      return;
    }

    container.innerHTML =
      filtered.map(product => `

        <div class="card">

          <img
            src="${product.image}"
            alt="${product.title}"
          >

          <div class="card-content">

            <div class="title">
              ${escapeHtml(product.title)}
            </div>

            <div class="price">
              ${Number(product.price).toLocaleString("ru-RU")} €
            </div>

            <div class="description">
              ${escapeHtml(product.description)}
            </div>

            <button
              class="delete"
              onclick="deleteProduct(${product.id})"
            >
              Удалить
            </button>

          </div>

        </div>

      `).join("");
  }

  function escapeHtml(text) {

    const div = document.createElement("div");
    div.textContent = text;

    return div.innerHTML;
  }

  renderProducts();

</script>

</body>
</html>
```
