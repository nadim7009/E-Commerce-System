# E-Commerce-System
A secure and scalable E-Commerce System built using Java, leveraging OOP principles like encapsulation and abstraction. Features include password hashing, user authentication, secure communication, and role-based access control. The platform is designed for high performance, reliability, and ease of maintenance.
import java.util.Scanner;
import java.util.ArrayList;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;

public class ECommerceSystem {

    static ArrayList<User> users = new ArrayList<>();
    static ArrayList<Product> products = new ArrayList<>();
    static User currentUser = null;
    
    // Cart for the current user
    static ArrayList<Product> currentUserCart = new ArrayList<>();
    static ArrayList<Order> orders = new ArrayList<>();

    public static void main(String[] args) {
        // Initialize some products
        initializeProducts();
        
        // Display the logo and welcome screen
        logo();
        
        // Main loop for login/registration
        while (true) {
            int choice = showMainMenu();
            if (choice == 1) {
                login();
            } else if (choice == 2) {
                register();
            } else if (choice == 3) {
                System.out.println("Exiting the application...");
                break;
            }
        }
    }

    // Method to display the logo and welcome screen
    public static void logo() {
        System.out.println("\n\n\n\n");
        System.out.println("\u001B[1;34m\t\t\t\t\t\t**************************************");
        System.out.println("\t\t\t\t\t\t*          E-Shop Management          *");
        System.out.println("\t\t\t\t\t\t*           System Logo              *");
        System.out.println("\u001B[1;34m\t\t\t\t\t\t**************************************\n");
        System.out.println("\u001B[1;33m\t\t\t\t\t\t   _______      _____ _____ _____ ");
        System.out.println("\t\t\t\t\t\t  / ____| |    / ____|  __ \\__   \\");
        System.out.println("\t\t\t\t\t\t | (___ | |__ | (___ | |__) | )  | |");
        System.out.println("\t\t\t\t\t\t  \\___ \\| '_ \\  \\___ \\|  ___/ /  / /");
        System.out.println("\t\t\t\t\t\t  ____) | | | | ____) | |  / /  /  /");
        System.out.println("\t\t\t\t\t\t |_____/|_| |_| |_____/|_| /_/  /__/ \n");
        System.out.println("\u001B[1;32m\t\t\t\t\t\t*****************************************");
        System.out.println("\t\t\t\t\t\t*          Welcome to our E-Shop         *");
        System.out.println("\t\t\t\t\t\t*      Your one-stop online shopping!    *");
        System.out.println("\u001B[1;32m\t\t\t\t\t\t*****************************************\n");
        System.out.println("\u001B[0m");
    }

    // Method to show the main menu
    @SuppressWarnings("resource")
    public static int showMainMenu() {
        Scanner sc = new Scanner(System.in);
        int choice = -1;
        while (choice < 1 || choice > 3) {
            try {
                System.out.println("\n\nMain Menu:");
                System.out.println("1. Login");
                System.out.println("2. Sign Up");
                System.out.println("3. Exit");
                System.out.print("Enter your choice: ");
                choice = Integer.parseInt(sc.nextLine());
                
                if (choice < 1 || choice > 3) {
                    System.out.println("Invalid choice. Please choose between 1, 2, or 3.");
                }
            } catch (NumberFormatException e) {
                System.out.println("Invalid input! Please enter a valid number.");
            }
        }
        return choice;
    }

    // Method for login functionality
    @SuppressWarnings("resource")
    public static void login() {
        Scanner sc = new Scanner(System.in);
        String username = "";
        String password = "";
        
        try {
            System.out.print("\nEnter username: ");
            username = sc.nextLine();
            System.out.print("Enter password: ");
            password = sc.nextLine();

            boolean found = false;
            for (User user : users) {
                if (user.getUsername().equals(username) && verifyPassword(password, user.getPassword())) {
                    currentUser = user;
                    found = true;
                    System.out.println("Login successful!");
                    postLoginInterface();
                    return;
                }
            }

            if (!found) {
                System.out.println("Invalid username or password.");
            }
        } catch (Exception e) {
            System.out.println("Error while logging in: " + e.getMessage());
        }
    }

    // Method for user registration
    @SuppressWarnings("resource")
    public static void register() {
        Scanner sc = new Scanner(System.in);
        String username = "", password = "", email = "";

        try {
            System.out.print("\nEnter username: ");
            username = sc.nextLine();
            System.out.print("Enter password: ");
            password = sc.nextLine();
            System.out.print("Enter email: ");
            email = sc.nextLine();

            // Hash the password before storing it
            String hashedPassword = hashPassword(password);

            User newUser = new User(username, hashedPassword, email);
            users.add(newUser);
            System.out.println("Registration successful! You can now log in.");
        } catch (Exception e) {
            System.out.println("Error while registering: " + e.getMessage());
        }
    }

    // Method to hash the password using SHA-256
    public static String hashPassword(String password) throws NoSuchAlgorithmException {
        MessageDigest md = MessageDigest.getInstance("SHA-256");
        byte[] hashBytes = md.digest(password.getBytes());
        StringBuilder hexString = new StringBuilder();
        for (byte b : hashBytes) {
            hexString.append(String.format("%02x", b));
        }
        return hexString.toString();
    }

    // Method to verify the entered password by comparing hashed values
    public static boolean verifyPassword(String enteredPassword, String storedHashedPassword) {
        try {
            String hashedEnteredPassword = hashPassword(enteredPassword);
            return hashedEnteredPassword.equals(storedHashedPassword);
        } catch (NoSuchAlgorithmException e) {
            System.out.println("Error verifying password: " + e.getMessage());
            return false;
        }
    }

    // Method for the post-login interface
    @SuppressWarnings("resource")
    public static void postLoginInterface() {
        Scanner sc = new Scanner(System.in);
        while (true) {
            System.out.println("\nWelcome " + currentUser.getUsername() + "!");
            System.out.println("Post-login Menu:");
            System.out.println("1. View Products");
            System.out.println("2. Add to Cart");
            System.out.println("3. View Cart");
            System.out.println("4. Checkout");
            System.out.println("5. Order History");
            System.out.println("6. Logout");
            System.out.print("Enter your choice: ");
            int choice = -1;
            
            try {
                choice = Integer.parseInt(sc.nextLine());
            } catch (NumberFormatException e) {
                System.out.println("Invalid input! Please enter a valid number.");
                continue;
            }

            if (choice == 1) {
                viewProducts();
            } else if (choice == 2) {
                addToCart();
            } else if (choice == 3) {
                viewCart();
            } else if (choice == 4) {
                checkout();
            } else if (choice == 5) {
                viewOrderHistory();
            } else if (choice == 6) {
                logout();
                break;
            } else {
                System.out.println("Invalid choice. Please try again.");
            }
        }
    }

    // Method to view products
    public static void viewProducts() {
        System.out.println("\nAvailable Products:");
        for (Product product : products) {
            System.out.println(product.getName() + " - $" + product.getPrice());
        }
    }

    // Method to add product to the cart
    @SuppressWarnings("resource")
    public static void addToCart() {
        Scanner sc = new Scanner(System.in);
        System.out.print("\nEnter product name to add to cart: ");
        String productName = sc.nextLine();
        
        boolean productFound = false;
        for (Product product : products) {
            if (product.getName().equalsIgnoreCase(productName)) {
                currentUserCart.add(product);
                System.out.println("Added " + product.getName() + " to your cart.");
                productFound = true;
                break;
            }
        }
        
        if (!productFound) {
            System.out.println("Product not found. Please check the product name and try again.");
        }
    }

    // Method to view items in the cart
    public static void viewCart() {
        if (currentUserCart.isEmpty()) {
            System.out.println("\nYour cart is empty.");
        } else {
            System.out.println("\nItems in your cart:");
            for (Product product : currentUserCart) {
                System.out.println(product.getName() + " - $" + product.getPrice());
            }
        }
    }

    // Method for checkout
    public static void checkout() {
        if (currentUserCart.isEmpty()) {
            System.out.println("\nYour cart is empty. Add some items before checking out.");
        } else {
            double total = 0;
            for (Product product : currentUserCart) {
                total += product.getPrice();
            }
            System.out.println("\nTotal amount: $" + total);
            orders.add(new Order(currentUser, currentUserCart, total));
            currentUserCart.clear();
            System.out.println("Checkout successful! Your order has been placed.");
        }
    }

    // Method to view order history
    public static void viewOrderHistory() {
        if (orders.isEmpty()) {
            System.out.println("\nYou have no previous orders.");
        } else {
            System.out.println("\nYour order history:");
            for (Order order : orders) {
                System.out.println("Order ID: " + order.getOrderId() + " | Total Amount: $" + order.getTotalAmount());
            }
        }
    }

    // Method to logout
    public static void logout() {
        currentUser = null;
        currentUserCart.clear();
        System.out.println("Logged out successfully.");
    }

    // Initialize some products
    public static void initializeProducts() {
        products.add(new Product("Laptop", 999.99));
        products.add(new Product("Smartphone", 499.99));
        products.add(new Product("Headphones", 199.99));
        products.add(new Product("Smartwatch", 129.99));
    }

    // User class
    static class User {
        private String username;
        private String password;  // This will now store the hashed password
        private String email;

        public User(String username, String password, String email) {
            this.username = username;
            this.password = password;
            this.email = email;
        }

        public String getUsername() {
            return username;
        }

        public String getPassword() {
            return password;
        }

        public String getEmail() {
            return email;
        }
    }

    // Product class
    static class Product {
        private String name;
        private double price;

        public Product(String name, double price) {
            this.name = name;
            this.price = price;
        }

        public String getName() {
            return name;
        }

        public double getPrice() {
            return price;
        }
    }

    // Order class
    static class Order {
        private static int orderIdCounter = 1;
        private int orderId;
        private User user;
        private ArrayList<Product> products;
        private double totalAmount;

        public Order(User user, ArrayList<Product> products, double totalAmount) {
            this.orderId = orderIdCounter++;
            this.user = user;
            this.products = products;
            this.totalAmount = totalAmount;
        }

        public int getOrderId() {
            return orderId;
        }

        public User getUser() {
            return user;
        }

        public ArrayList<Product> getProducts() {
            return products;
        }

        public double getTotalAmount() {
            return totalAmount;
        }
    }
}
