import java.util.*;

class Stock {
    private String name;
    private double price;

    public Stock(String name, double price) {
        this.name = name;
        this.price = price;
    }

    public String getName() {
        return name;
    }

    public double getPrice() {
        return price;
    }

    public void setPrice(double price) {
        this.price = price;
    }
}

class Portfolio {
    private Map<String, Integer> stocks;
    private double balance;

    public Portfolio(double initialBalance) {
        this.stocks = new HashMap<>();
        this.balance = initialBalance;
    }

    public double getBalance() {
        return balance;
    }

    public void addStock(String name, int quantity) {
        stocks.put(name, stocks.getOrDefault(name, 0) + quantity);
    }

    public void removeStock(String name, int quantity) {
        if (stocks.containsKey(name)) {
            int currentQuantity = stocks.get(name);
            if (currentQuantity <= quantity) {
                stocks.remove(name);
            } else {
                stocks.put(name, currentQuantity - quantity);
            }
        }
    }

    public int getStockQuantity(String name) {
        return stocks.getOrDefault(name, 0);
    }

    public Set<String> getStockNames() {
        return stocks.keySet();
    }

    public void updateBalance(double amount) {
        this.balance += amount;
    }
}

class Market {
    private Map<String, Stock> stocks;

    public Market() {
        stocks = new HashMap<>();
        generateMarketData();
    }

    private void generateMarketData() {
        stocks.put("Apple", new Stock("Apple", 150.0));
        stocks.put("Google", new Stock("Google", 2800.0));
        stocks.put("Amazon", new Stock("Amazon", 3400.0));
        stocks.put("Tesla", new Stock("Tesla", 700.0));
    }

    public Stock getStock(String name) {
        return stocks.get(name);
    }

    public void updateMarketData() {
        Random rand = new Random();
        for (Stock stock : stocks.values()) {
            double change = rand.nextDouble() * 20 - 10;
            stock.setPrice(stock.getPrice() + change);
        }
    }

    public void displayMarketData() {
        System.out.println("Market Data:");
        for (Stock stock : stocks.values()) {
            System.out.println(stock.getName() + ": $" + stock.getPrice());
        }
    }
}

public class TradingPlatform {
    private Portfolio portfolio;
    private Market market;
    private Scanner scanner;

    public TradingPlatform(double initialBalance) {
        this.portfolio = new Portfolio(initialBalance);
        this.market = new Market();
        this.scanner = new Scanner(System.in);
    }

    public void start() {
        while(true) {
            market.updateMarketData();
            market.displayMarketData();
            displayPortfolio();
            displayMenu();
            String choice = scanner.nextLine();
            handleMenuChoice(choice);
        }
    }

    private void displayPortfolio() {
        System.out.println("\nYour Portfolio:");
        System.out.println("Balance: $" + portfolio.getBalance());
        for (String stockName : portfolio.getStockNames()) {
            System.out.println(stockName + ": " + portfolio.getStockQuantity(stockName) + " shares");
        }
        System.out.println();
    }

    private void displayMenu() {
        System.out.println("Menu:");
        System.out.println("1. Buy Stock");
        System.out.println("2. Sell Stock");
        System.out.println("3. Exit");
        System.out.print("Enter your choice: ");
    }

    private void handleMenuChoice(String choice) {
        switch (choice) {
            case "1":
                buyStock();
                break;
            case "2":
                sellStock();
                break;
            case "3":
                System.out.println("Exiting the platform. Goodbye!");
                System.exit(0);
                break;
            default:
                System.out.println("Invalid choice. Please try again.");
        }
    }

    private void buyStock() {
        System.out.print("Enter stock name: ");
        String stockName = scanner.nextLine();
        Stock stock = market.getStock(stockName);
        if (stock == null) {
            System.out.println("Stock not found.");
            return;
        }
        System.out.print("Enter quantity: ");
        int quantity = Integer.parseInt(scanner.nextLine());
        double cost = stock.getPrice() * quantity;
        if (cost > portfolio.getBalance()) {
            System.out.println("Insufficient balance.");
        } else {
            portfolio.addStock(stockName, quantity);
            portfolio.updateBalance(-cost);
            System.out.println("Bought " + quantity + " shares of " + stockName);
        }
    }

    private void sellStock() {
        System.out.print("Enter stock name: ");
        String stockName = scanner.nextLine();
        int quantity = portfolio.getStockQuantity(stockName);
        if (quantity == 0) {
            System.out.println("You do not own any shares of " + stockName);
            return;
        }
        System.out.print("Enter quantity to sell: ");
        int sellQuantity = Integer.parseInt(scanner.nextLine());
        if (sellQuantity > quantity) {
            System.out.println("You do not have enough shares to sell.");
            return;
        }
        Stock stock = market.getStock(stockName);
        double revenue = stock.getPrice() * sellQuantity;
        portfolio.removeStock(stockName, sellQuantity);
        portfolio.updateBalance(revenue);
        System.out.println("Sold " + sellQuantity + " shares of " + stockName);
    }

    public static void main(String[] args) {
        TradingPlatform platform = new TradingPlatform(1000000.0);
        platform.start();
    }
}
