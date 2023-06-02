# 3hours


<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>
    <h1>Sharpener</h1>
    <form>
        <label for="product">Product</label>
        <input type="text" id="product">
        <label for="price">Price</label>
        <input type="number" id="price">
        <label for="quantity">Quantity</label>
        <input type="number" id="quantity">
        <button type="button" onclick="addProduct()">AddProduct</button>
        
    </form>
    <ul id="list"></ul>
    <p>
    <h2>Total Price: <span id="total"></h2></span></p>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/axios/1.4.0/axios.min.js"></script>
    <script src="index1.js"></script>

    <script>
        const products = [];

async function addProduct() {
  const product = document.getElementById("product").value;
  const price = Number(document.getElementById("price").value);
  const quantity = Number(document.getElementById("quantity").value);
  const total = price * quantity;
  const item = { product, price, quantity, total };
  products.push(item);
  document.getElementById("product").value = "";
  document.getElementById("price").value = "";
  document.getElementById("quantity").value = "";
  displayProducts();

  try {
    const response = await axios.post("https://crudcrud.com/api/f3592145226a4e4cafb06cba75494a5d/E-Com", item);
    item._id = response.data._id; // Assign the generated ID from the response to the item
    console.log("Send request fulfilled");
  } catch (error) {
    console.log("Send request failed", error);
    alert("An error occurred while sending the data.");
  }

  // Save data in local storage
  localStorage.setItem("products", JSON.stringify(products));
}

function displayProducts() {
  const list = document.getElementById("list");
  list.innerHTML = "";
  let totalPrice = 0;

  products.forEach(function (item, index) {
    const listItem = document.createElement("li");
    const text = `${item.product}: ${item.price} x ${item.quantity} = ${item.total}`;
    listItem.innerText = text;

    const deleteButton = document.createElement("button");
    deleteButton.innerText = "Delete";
    deleteButton.addEventListener("click", async function () {
      products.splice(index, 1);
      displayProducts();
      try {
        await axios.delete(`https://crudcrud.com/api/f3592145226a4e4cafb06cba75494a5d/E-Com/${item._id}`);
        console.log("Delete request sent");
      } catch (error) {
        console.log("Delete request failed", error);
        alert("An error occurred while deleting the data.");
      }
    });

    listItem.appendChild(deleteButton);
    list.appendChild(listItem);
    totalPrice += item.total;
  });

  document.getElementById("total").innerText = `${totalPrice}`;
}

// Load data from local storage on page load
window.addEventListener("load", function () {
  const storedProducts = localStorage.getItem("products");
  if (storedProducts) {
    products.push(...JSON.parse(storedProducts));
  }
  displayProducts();
});

    </script>
</body>

</html>