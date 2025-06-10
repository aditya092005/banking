import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.*;

public class BankingSystemGUI {
    private JFrame frame;
    private JTextField loginField, passwordField, accountNumField, accountNameField, newPasswordField, depositField, withdrawField;
    private JTextArea outputArea, historyArea;
    private JLabel statusLabel;
    private JCheckBox darkThemeToggle;
    private JLabel logoLabel;
    private JLabel welcomeLabel;

    private HashMap<String, Account> accounts = new HashMap<>();
    private String loggedInAccount = null;

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> new BankingSystemGUI().createLoginGUI());
    }

    public void createLoginGUI() {
        frame = new JFrame("Banking Management System - Login");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(400, 350);
        frame.setLayout(new GridLayout(7, 2, 10, 10));

        loginField = new JTextField();
        passwordField = new JTextField();
        JButton loginBtn = new JButton("Login");
        JButton goToCreateBtn = new JButton("Create New Account");
        statusLabel = new JLabel("Status: Please login or create an account");
        darkThemeToggle = new JCheckBox("Dark Theme");

        logoLabel = new JLabel("Smart Thinkers", SwingConstants.CENTER);
        logoLabel.setFont(new Font("Arial", Font.BOLD, 18));
        logoLabel.setForeground(Color.BLUE);

        frame.add(logoLabel);
        frame.add(new JLabel());
        frame.add(new JLabel("Account Number:"));
        frame.add(loginField);
        frame.add(new JLabel("Password:"));
        frame.add(passwordField);
        frame.add(loginBtn);
        frame.add(goToCreateBtn);
        frame.add(new JLabel());
        frame.add(darkThemeToggle);
        frame.add(statusLabel);

        loginBtn.addActionListener(e -> login());
        goToCreateBtn.addActionListener(e -> createAccountPage());
        darkThemeToggle.addActionListener(e -> toggleDarkTheme());

        frame.setVisible(true);
    }

    public void createAccountPage() {
        frame.getContentPane().removeAll();
        frame.setLayout(new GridLayout(7, 2, 10, 10));
        frame.setSize(400, 400);

        accountNumField = new JTextField();
        accountNameField = new JTextField();
        newPasswordField = new JTextField();
        JButton createBtn = new JButton("Create Account");
        JButton backBtn = new JButton("Back to Login");

        logoLabel = new JLabel("Smart Thinkers", SwingConstants.CENTER);
        logoLabel.setFont(new Font("Arial", Font.BOLD, 18));
        logoLabel.setForeground(Color.BLUE);

        frame.add(logoLabel);
        frame.add(new JLabel());
        frame.add(new JLabel("New Account Number:"));
        frame.add(accountNumField);
        frame.add(new JLabel("Account Holder Name:"));
        frame.add(accountNameField);
        frame.add(new JLabel("Password:"));
        frame.add(newPasswordField);
        frame.add(createBtn);
        frame.add(backBtn);
        frame.add(new JLabel());
        frame.add(darkThemeToggle);
        frame.add(statusLabel);

        createBtn.addActionListener(e -> handleAccountCreation());
        backBtn.addActionListener(e -> createLoginGUI());
        darkThemeToggle.addActionListener(e -> toggleDarkTheme());

        frame.revalidate();
        frame.repaint();
    }

    private void handleAccountCreation() {
        String accNum = accountNumField.getText();
        String name = accountNameField.getText();
        String pwd = newPasswordField.getText();

        if (accNum.isEmpty() || name.isEmpty() || pwd.isEmpty()) {
            statusLabel.setText("Status: All fields are required!");
            return;
        }

        if (accounts.containsKey(accNum)) {
            statusLabel.setText("Status: Account already exists!");
        } else {
            accounts.put(accNum, new Account(accNum, name, pwd));
            statusLabel.setText("Status: Account created successfully!");
            createLoginGUI();
        }
    }

    private void login() {
        String accNum = loginField.getText();
        String pwd = passwordField.getText();

        if (!accounts.containsKey(accNum)) {
            statusLabel.setText("Status: Account not found!");
            return;
        }

        Account acc = accounts.get(accNum);
        if (!acc.password.equals(pwd)) {
            statusLabel.setText("Status: Incorrect password!");
            return;
        }

        loggedInAccount = accNum;
        JOptionPane.showMessageDialog(frame, "Welcome Mr./Mrs. " + acc.holderName + "!");
        createMainDashboard();
    }

    private void createMainDashboard() {
        frame.getContentPane().removeAll();
        frame.setLayout(new BorderLayout(10, 10));

        JPanel mainPanel = new JPanel();
        mainPanel.setLayout(new BoxLayout(mainPanel, BoxLayout.Y_AXIS));
        mainPanel.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10));

        JPanel controlPanel = new JPanel(new GridLayout(3, 2, 10, 10));
        controlPanel.setMaximumSize(new Dimension(500, 100));

        depositField = new JTextField();
        withdrawField = new JTextField();
        JButton depositBtn = new JButton("Deposit");
        JButton withdrawBtn = new JButton("Withdraw");
        JButton viewBtn = new JButton("View Account");

        controlPanel.add(new JLabel("Deposit Amount:"));
        controlPanel.add(depositField);
        controlPanel.add(new JLabel("Withdraw Amount:"));
        controlPanel.add(withdrawField);
        controlPanel.add(depositBtn);
        controlPanel.add(withdrawBtn);

        outputArea = new JTextArea(8, 40);
        outputArea.setEditable(false);
        outputArea.setBorder(BorderFactory.createTitledBorder("Account Info"));

        historyArea = new JTextArea(6, 40);
        historyArea.setEditable(false);
        historyArea.setBorder(BorderFactory.createTitledBorder("Transaction History"));

        JScrollPane outputScrollPane = new JScrollPane(outputArea);
        JScrollPane historyScrollPane = new JScrollPane(historyArea);

        welcomeLabel = new JLabel("Welcome, " + accounts.get(loggedInAccount).holderName, SwingConstants.LEFT);
        welcomeLabel.setFont(new Font("Arial", Font.BOLD, 16));
        welcomeLabel.setForeground(Color.BLUE);

        mainPanel.add(welcomeLabel);
        mainPanel.add(Box.createRigidArea(new Dimension(0, 10)));
        mainPanel.add(controlPanel);
        mainPanel.add(Box.createRigidArea(new Dimension(0, 10)));
        mainPanel.add(viewBtn);
        mainPanel.add(Box.createRigidArea(new Dimension(0, 10)));
        mainPanel.add(outputScrollPane);
        mainPanel.add(Box.createRigidArea(new Dimension(0, 10)));
        mainPanel.add(historyScrollPane);
        mainPanel.add(Box.createRigidArea(new Dimension(0, 10)));
        mainPanel.add(darkThemeToggle);

        frame.add(mainPanel, BorderLayout.CENTER);

        depositBtn.addActionListener(e -> deposit());
        withdrawBtn.addActionListener(e -> withdraw());
        viewBtn.addActionListener(e -> viewAccount());
        darkThemeToggle.addActionListener(e -> toggleDarkTheme());

        frame.revalidate();
        frame.repaint();
    }

    private void deposit() {
        try {
            double amt = Double.parseDouble(depositField.getText());
            accounts.get(loggedInAccount).deposit(amt);
            outputArea.setText("Deposited Rs." + amt);
            updateHistory("Deposited Rs." + amt);
        } catch (Exception e) {
            outputArea.setText("Invalid amount!");
        }
    }

    private void withdraw() {
        try {
            double amt = Double.parseDouble(withdrawField.getText());
            boolean success = accounts.get(loggedInAccount).withdraw(amt);
            if (success) {
                outputArea.setText("Withdrawn Rs." + amt);
                updateHistory("Withdrawn Rs." + amt);
            } else {
                outputArea.setText("Insufficient balance.");
            }
        } catch (Exception e) {
            outputArea.setText("Invalid amount!");
        }
    }

    private void viewAccount() {
        outputArea.setText(accounts.get(loggedInAccount).toString());
    }

    private void updateHistory(String entry) {
        Account acc = accounts.get(loggedInAccount);
        acc.transactionHistory.add(entry);
        StringBuilder historyText = new StringBuilder();
        for (String line : acc.transactionHistory) {
            historyText.append(line).append("\n");
        }
        historyArea.setText(historyText.toString());
    }

    private void toggleDarkTheme() {
        boolean dark = darkThemeToggle.isSelected();
        Color bg = dark ? Color.DARK_GRAY : Color.WHITE;
        Color fg = dark ? Color.WHITE : Color.BLACK;
        Color fieldBg = dark ? new Color(60, 63, 65) : Color.WHITE;

        frame.getContentPane().setBackground(bg);
        for (Component comp : frame.getContentPane().getComponents()) {
            updateComponentTheme(comp, bg, fg, fieldBg);
        }

        if (outputArea != null) {
            outputArea.setBackground(fieldBg);
            outputArea.setForeground(fg);
        }
        if (historyArea != null) {
            historyArea.setBackground(fieldBg);
            historyArea.setForeground(fg);
        }
        if (statusLabel != null)
            statusLabel.setForeground(fg);
        if (logoLabel != null)
            logoLabel.setForeground(dark ? Color.CYAN : Color.BLUE);
        if (welcomeLabel != null)
            welcomeLabel.setForeground(dark ? Color.CYAN : Color.BLUE);
    }

    private void updateComponentTheme(Component comp, Color bg, Color fg, Color fieldBg) {
        if (comp instanceof JPanel) {
            comp.setBackground(bg);
            for (Component child : ((JPanel) comp).getComponents()) {
                updateComponentTheme(child, bg, fg, fieldBg);
            }
        } else if (comp instanceof JLabel || comp instanceof JCheckBox) {
            comp.setForeground(fg);
        } else if (comp instanceof JButton) {
            comp.setBackground(bg);
            comp.setForeground(fg);
        } else if (comp instanceof JTextField || comp instanceof JTextArea) {
            comp.setBackground(fieldBg);
            comp.setForeground(fg);
        }
    }

    class Account {
        private String accountNumber;
        private String holderName;
        private String password;
        private double balance;
        private ArrayList<String> transactionHistory;

        public Account(String accNum, String name, String pwd) {
            this.accountNumber = accNum;
            this.holderName = name;
            this.password = pwd;
            this.balance = 0;
            this.transactionHistory = new ArrayList<>();
        }

        public void deposit(double amount) {
            this.balance += amount;
            transactionHistory.add("Deposited Rs." + amount);
        }

        public boolean withdraw(double amount) {
            if (amount > balance) return false;
            this.balance -= amount;
            transactionHistory.add("Withdrawn Rs." + amount);
            return true;
        }

        public String toString() {
            return "\n========= ACCOUNT DETAILS =========\n" +
                   "Account Number: " + accountNumber +
                   "\nName: " + holderName +
                   "\nCurrent Balance: Rs." + balance +
                   "\n====================================";
        }
    }
}
