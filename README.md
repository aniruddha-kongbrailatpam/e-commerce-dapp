# e-commerce-dapp

pragma solidity ^0.8.0;

contract Ecommerce {
    // Define the product structure
    struct Product {
        uint256 id;
        string name;
        uint256 price;
        string imageUrl;
        string description;
        uint256 quantity;
    }

    // Store the product information
    mapping(uint256 => Product) public products;
    uint256 public productCount;

    // Store the order information
    struct Order {
        uint256 id;
        address buyer;
        uint256 productId;
        uint256 quantity;
        uint256 timestamp;
    }

    // Store the order information
    mapping(uint256 => Order) public orders;
    uint256 public orderCount;

    // Event to track purchases
    event Purchase(uint256 indexed id, address buyer, uint256 productId, uint256 quantity, uint256 timestamp);

    // Function to add a new product
    function addProduct(string memory name, uint256 price, string memory imageUrl, string memory description, uint256 quantity) public {
        // Increment the product count
        productCount++;
        // Add the product information to the mapping
        products[productCount] = Product(productCount, name, price, imageUrl, description, quantity);
    }

    // Function to place an order
    function placeOrder(uint256 productId, uint256 quantity) public payable {
        // Retrieve the product information
        Product storage product = products[productId];
        // Check if the product is in stock
        require(product.quantity >= quantity, "Not enough products in stock");
        // Check if the buyer has sent enough Ether to cover the cost of the order
        require(msg.value >= product.price * quantity, "Not enough Ether sent");
        // Update the product quantity
        product.quantity -= quantity;
        // Increment the order count
        orderCount++;
        // Add the order information to the mapping
        orders[orderCount] = Order(orderCount, msg.sender, productId, quantity, now);
        // Emit the Purchase event
        emit Purchase(orderCount, msg.sender, productId, quantity, now);
        // Transfer the Ether to the seller
        product.seller.transfer(msg.value);
    }
}
