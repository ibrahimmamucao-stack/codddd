package staffmanagementsystem;

import javax.swing.*;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.*;
import java.time.LocalDate;
import java.time.format.DateTimeParseException;
import java.util.HashMap;
import java.util.List;
import java.util.ArrayList;
import java.util.Map;

class LeaveRequest implements Serializable {
    private static final long serialVersionUID = 1L;
    private LocalDate startDate;
    private LocalDate endDate;
    private String reason;
    private String status;

    public LeaveRequest(LocalDate startDate, LocalDate endDate, String reason) {
        this.startDate = startDate;
        this.endDate = endDate;
        this.reason = reason;
        this.status = "Pending";
    }

    public LocalDate getStartDate() { return startDate; }
    public LocalDate getEndDate() { return endDate; }
    public String getReason() { return reason; }
    public String getStatus() { return status; }
    public void setStatus(String status) { this.status = status; }
}

class Staff implements Serializable {
    private static final long serialVersionUID = 1L;
    private int id;
    private String name;
    private String position;
    private double salary;
    private Map<LocalDate, String> attendance;
    private List<LeaveRequest> leaveRequests;

    public Staff(int id, String name, String position, double salary) {
        this.id = id;
        this.name = name;
        this.position = position;
        this.salary = salary;
        this.attendance = new HashMap<>();
        this.leaveRequests = new ArrayList<>();
    }

    public int getId() { return id; }
    public String getName() { return name; }
    public String getPosition() { return position; }
    public double getSalary() { return salary; }

    public void setName(String name) { this.name = name; }
    public void setPosition(String position) { this.position = position; }
    public void setSalary(double salary) { this.salary = salary; }

    public Map<LocalDate, String> getAttendance() { return attendance; }
    public void markAttendance(LocalDate date, String status) {
        attendance.put(date, status);
    }

    public List<LeaveRequest> getLeaveRequests() { return leaveRequests; }
    public void addLeaveRequest(LeaveRequest request) {
        leaveRequests.add(request);
    }
}

class StaffManager {
    private List<Staff> staffList = new ArrayList<>();
    private int nextId = 1;
    private static final String DATA_FILE = "staffData.ser";

    public StaffManager() {
        loadData();
        if (staffList.isEmpty()) {
            nextId = 1;
        } else {
            nextId = staffList.stream().mapToInt(Staff::getId).max().orElse(0) + 1;
        }
    }

    public void addStaff(String name, String position, double salary) {
        staffList.add(new Staff(nextId++, name, position, salary));
        saveData();
    }

    public List<Staff> getAllStaff() { return staffList; }

    public boolean updateStaff(int id, String name, String position, double salary) {
        Staff staff = findStaffById(id);
        if (staff == null) return false;
        staff.setName(name);
        staff.setPosition(position);
        staff.setSalary(salary);
        saveData();
        return true;
    }

    public boolean deleteStaff(int id) {
        boolean removed = staffList.removeIf(s -> s.getId() == id);
        if (removed) saveData();
        return removed;
    }

    public Staff findStaffById(int id) {
        return staffList.stream().filter(s -> s.getId() == id).findFirst().orElse(null);
    }

    public boolean markAttendance(int id, LocalDate date, String status) {
        Staff staff = findStaffById(id);
        if (staff == null) return false;
        staff.markAttendance(date, status);
        saveData();
        return true;
    }

    public boolean addLeaveRequest(int id, LocalDate start, LocalDate end, String reason) {
        Staff staff = findStaffById(id);
        if (staff == null) return false;
        staff.addLeaveRequest(new LeaveRequest(start, end, reason));
        saveData();
        return true;
    }

    public boolean approveLeaveRequest(int staffId, int requestIndex) {
        Staff staff = findStaffById(staffId);
        if (staff == null || requestIndex < 0 || requestIndex >= staff.getLeaveRequests().size()) return false;
        staff.getLeaveRequests().get(requestIndex).setStatus("Approved");
        saveData();
        return true;
    }

    public boolean rejectLeaveRequest(int staffId, int requestIndex) {
        Staff staff = findStaffById(staffId);
        if (staff == null || requestIndex < 0 || requestIndex >= staff.getLeaveRequests().size()) return false;
        staff.getLeaveRequests().get(requestIndex).setStatus("Rejected");
        saveData();
        return true;
    }

    private void saveData() {
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(DATA_FILE))) {
            oos.writeObject(staffList);
        } catch (IOException e) {
            JOptionPane.showMessageDialog(null, "Error saving data: " + e.getMessage());
        }
    }

    @SuppressWarnings("unchecked")
    private void loadData() {
        File file = new File(DATA_FILE);
        if (!file.exists()) {
            return;
        }
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(DATA_FILE))) {
            staffList = (List<Staff>) ois.readObject();
        } catch (FileNotFoundException e) {
            // File doesn't exist yet, start with empty list
        } catch (IOException | ClassNotFoundException e) {
            JOptionPane.showMessageDialog(null, "Error loading data: " + e.getMessage());
        }
    }
}

class LoginSystem extends JFrame {
    private static final long serialVersionUID = 1L;
    private JTextField usernameField;
    private JPasswordField passwordField;
    private JButton loginButton;
    private JLabel messageLabel;

    // Simulated user database (username -> {password, role, staffId})
    private static final Map<String, String[]> users = new HashMap<>();
    static {
        users.put("superadmin", new String[]{"superpass", "super admin", "-1"});
        users.put("admin", new String[]{"adminpass", "admin", "-1"});
        users.put("user", new String[]{"userpass", "user", "1"});
        users.put("john", new String[]{"john123", "user", "2"});
    }

    public LoginSystem() {
        setTitle("Login System");
        setSize(400, 250);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
        
        JPanel mainPanel = new JPanel();
        mainPanel.setLayout(new BoxLayout(mainPanel, BoxLayout.Y_AXIS));
        mainPanel.setBorder(BorderFactory.createEmptyBorder(20, 30, 20, 30));

        JLabel titleLabel = new JLabel("Staff Management System");
        titleLabel.setFont(new Font("Arial", Font.BOLD, 18));
        titleLabel.setAlignmentX(Component.CENTER_ALIGNMENT);
        
        JPanel formPanel = new JPanel(new GridLayout(2, 2, 10, 10));
        formPanel.setMaximumSize(new Dimension(300, 80));
        
        usernameField = new JTextField();
        passwordField = new JPasswordField();
        
        formPanel.add(new JLabel("Username:"));
        formPanel.add(usernameField);
        formPanel.add(new JLabel("Password:"));
        formPanel.add(passwordField);

        loginButton = new JButton("Login");
        loginButton.setAlignmentX(Component.CENTER_ALIGNMENT);
        
        messageLabel = new JLabel(" ");
        messageLabel.setAlignmentX(Component.CENTER_ALIGNMENT);
        messageLabel.setForeground(Color.RED);

        mainPanel.add(titleLabel);
        mainPanel.add(Box.createRigidArea(new Dimension(0, 20)));
        mainPanel.add(formPanel);
        mainPanel.add(Box.createRigidArea(new Dimension(0, 15)));
        mainPanel.add(loginButton);
        mainPanel.add(Box.createRigidArea(new Dimension(0, 10)));
        mainPanel.add(messageLabel);

        add(mainPanel);

        loginButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String username = usernameField.getText().trim();
                String password = new String(passwordField.getPassword());

                if (username.isEmpty() || password.isEmpty()) {
                    messageLabel.setText("Please fill in all fields.");
                    return;
                }

                if (users.containsKey(username)) {
                    if (users.get(username)[0].equals(password)) {
                        String role = users.get(username)[1];
                        int staffId = Integer.parseInt(users.get(username)[2]);
                        messageLabel.setText("Login successful!");
                        openDashboard(role, staffId, username);
                    } else {
                        messageLabel.setText("Invalid password.");
                    }
                } else {
                    messageLabel.setText("Invalid username.");
                }
            }
        });
        
        // Allow Enter key to login
        passwordField.addActionListener(e -> loginButton.doClick());

        setVisible(true);
    }

    private void openDashboard(String role, int staffId, String username) {
        if (role.equals("user")) {
            new UserDashboard(staffId, username).setVisible(true);
        } else {
            new StaffGUI(role).setVisible(true);
        }
        this.dispose();
    }
}

class UserDashboard extends JFrame {
    private static final long serialVersionUID = 1L;
    private StaffManager manager;
    private int userId;
    private String username;
    private JTable attendanceTable, leaveTable;
    private DefaultTableModel attendanceModel, leaveModel;
    private JLabel nameLabel, positionLabel, attendanceCountLabel;

    public UserDashboard(int userId, String username) {
        this.userId = userId;
        this.username = username;
        this.manager = new StaffManager();
        setupUI();
        loadUserData();
    }

    private void setupUI() {
        setTitle("Employee Dashboard - " + username);
        setSize(800, 600);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);

        JPanel mainPanel = new JPanel(new BorderLayout(10, 10));
        mainPanel.setBorder(BorderFactory.createEmptyBorder(15, 15, 15, 15));

        // Header Panel
        JPanel headerPanel = createHeaderPanel();
        mainPanel.add(headerPanel, BorderLayout.NORTH);

        // Tabbed Pane for Attendance and Leave
        JTabbedPane tabbedPane = new JTabbedPane();
        tabbedPane.addTab("Attendance", createAttendancePanel());
        tabbedPane.addTab("Leave Requests", createLeavePanel());

        mainPanel.add(tabbedPane, BorderLayout.CENTER);

        // Logout Button
        JPanel bottomPanel = new JPanel(new FlowLayout(FlowLayout.RIGHT));
        JButton logoutBtn = new JButton("Logout");
        logoutBtn.addActionListener(e -> {
            this.dispose();
            new LoginSystem();
        });
        bottomPanel.add(logoutBtn);
        mainPanel.add(bottomPanel, BorderLayout.SOUTH);

        add(mainPanel);
    }

    private JPanel createHeaderPanel() {
        JPanel panel = new JPanel(new BorderLayout());
        panel.setBorder(BorderFactory.createCompoundBorder(
            BorderFactory.createLineBorder(Color.LIGHT_GRAY),
            BorderFactory.createEmptyBorder(15, 15, 15, 15)
        ));
        panel.setBackground(new Color(240, 248, 255));

        JPanel infoPanel = new JPanel(new GridLayout(3, 1, 5, 5));
        infoPanel.setBackground(new Color(240, 248, 255));
        
        nameLabel = new JLabel("Name: Loading...");
        positionLabel = new JLabel("Position: Loading...");
        attendanceCountLabel = new JLabel("Days Present This Month: 0");
        
        Font labelFont = new Font("Arial", Font.PLAIN, 14);
        nameLabel.setFont(labelFont);
        positionLabel.setFont(labelFont);
        attendanceCountLabel.setFont(labelFont);

        infoPanel.add(nameLabel);
        infoPanel.add(positionLabel);
        infoPanel.add(attendanceCountLabel);

        panel.add(infoPanel, BorderLayout.CENTER);

        return panel;
    }

    private JPanel createAttendancePanel() {
        JPanel panel = new JPanel(new BorderLayout(10, 10));
        panel.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10));

        // Title
        JLabel titleLabel = new JLabel("My Attendance History");
        titleLabel.setFont(new Font("Arial", Font.BOLD, 16));
        panel.add(titleLabel, BorderLayout.NORTH);

        // Table
        attendanceModel = new DefaultTableModel(new String[]{"Date", "Status"}, 0) {
            private static final long serialVersionUID = 1L;
            @Override
            public boolean isCellEditable(int row, int column) {
                return false;
            }
        };
        attendanceTable = new JTable(attendanceModel);
        attendanceTable.getTableHeader().setFont(new Font("Arial", Font.BOLD, 12));
        JScrollPane scrollPane = new JScrollPane(attendanceTable);
        panel.add(scrollPane, BorderLayout.CENTER);

        // Mark Attendance Button
        JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 10, 10));
        JButton markPresentBtn = new JButton("Mark Present Today");
        JButton markAbsentBtn = new JButton("Mark Absent Today");
        
        markPresentBtn.setPreferredSize(new Dimension(150, 35));
        markAbsentBtn.setPreferredSize(new Dimension(150, 35));
        
        markPresentBtn.addActionListener(e -> markAttendance("Present"));
        markAbsentBtn.addActionListener(e -> markAttendance("Absent"));

        buttonPanel.add(markPresentBtn);
        buttonPanel.add(markAbsentBtn);
        panel.add(buttonPanel, BorderLayout.SOUTH);

        return panel;
    }

    private JPanel createLeavePanel() {
        JPanel panel = new JPanel(new BorderLayout(10, 10));
        panel.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10));

        // Title
        JLabel titleLabel = new JLabel("My Leave Requests");
        titleLabel.setFont(new Font("Arial", Font.BOLD, 16));
        panel.add(titleLabel, BorderLayout.NORTH);

        // Table
        leaveModel = new DefaultTableModel(new String[]{"Start Date", "End Date", "Reason", "Status"}, 0) {
            private static final long serialVersionUID = 1L;
            @Override
            public boolean isCellEditable(int row, int column) {
                return false;
            }
        };
        leaveTable = new JTable(leaveModel);
        leaveTable.getTableHeader().setFont(new Font("Arial", Font.BOLD, 12));
        JScrollPane scrollPane = new JScrollPane(leaveTable);
        panel.add(scrollPane, BorderLayout.CENTER);

        // Submit Leave Button
        JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER));
        JButton submitLeaveBtn = new JButton("Submit New Leave Request");
        submitLeaveBtn.setPreferredSize(new Dimension(200, 35));
        submitLeaveBtn.addActionListener(e -> showLeaveRequestDialog());
        buttonPanel.add(submitLeaveBtn);
        panel.add(buttonPanel, BorderLayout.SOUTH);

        return panel;
    }

    private void loadUserData() {
        Staff staff = manager.findStaffById(userId);
        if (staff != null) {
            nameLabel.setText("Name: " + staff.getName());
            positionLabel.setText("Position: " + staff.getPosition());
            
            // Load attendance
            attendanceModel.setRowCount(0);
            List<Map.Entry<LocalDate, String>> sortedAttendance = new ArrayList<>(staff.getAttendance().entrySet());
            sortedAttendance.sort((a, b) -> b.getKey().compareTo(a.getKey())); // Most recent first
            
            int presentCount = 0;
            LocalDate now = LocalDate.now();
            for (Map.Entry<LocalDate, String> entry : sortedAttendance) {
                attendanceModel.addRow(new Object[]{entry.getKey(), entry.getValue()});
                if (entry.getKey().getMonth() == now.getMonth() && 
                    entry.getKey().getYear() == now.getYear() && 
                    entry.getValue().equalsIgnoreCase("Present")) {
                    presentCount++;
                }
            }
            attendanceCountLabel.setText("Days Present This Month: " + presentCount);

            // Load leave requests
            leaveModel.setRowCount(0);
            for (LeaveRequest req : staff.getLeaveRequests()) {
                leaveModel.addRow(new Object[]{
                    req.getStartDate(),
                    req.getEndDate(),
                    req.getReason(),
                    req.getStatus()
                });
            }
        } else {
            JOptionPane.showMessageDialog(this, 
                "User data not found. Please contact administrator.", 
                "Error", 
                JOptionPane.ERROR_MESSAGE);
        }
    }

    private void markAttendance(String status) {
        LocalDate today = LocalDate.now();
        Staff staff = manager.findStaffById(userId);
        
        if (staff != null && staff.getAttendance().containsKey(today)) {
            JOptionPane.showMessageDialog(this,
                "Attendance already marked for today!",
                "Already Marked",
                JOptionPane.WARNING_MESSAGE);
            return;
        }

        if (manager.markAttendance(userId, today, status)) {
            JOptionPane.showMessageDialog(this,
                "Attendance marked as " + status + " for " + today,
                "Success",
                JOptionPane.INFORMATION_MESSAGE);
            loadUserData();
        } else {
            JOptionPane.showMessageDialog(this,
                "Failed to mark attendance",
                "Error",
                JOptionPane.ERROR_MESSAGE);
        }
    }

    private void showLeaveRequestDialog() {
        JDialog dialog = new JDialog(this, "Submit Leave Request", true);
        dialog.setSize(400, 300);
        dialog.setLocationRelativeTo(this);

        JPanel panel = new JPanel(new GridLayout(4, 2, 10, 10));
        panel.setBorder(BorderFactory.createEmptyBorder(20, 20, 20, 20));

        JTextField startField = new JTextField();
        JTextField endField = new JTextField();
        JTextArea reasonArea = new JTextArea(3, 20);
        reasonArea.setLineWrap(true);
        reasonArea.setWrapStyleWord(true);
        JScrollPane reasonScroll = new JScrollPane(reasonArea);

        panel.add(new JLabel("Start Date (YYYY-MM-DD):"));
        panel.add(startField);
        panel.add(new JLabel("End Date (YYYY-MM-DD):"));
        panel.add(endField);
        panel.add(new JLabel("Reason:"));
        panel.add(reasonScroll);

        JPanel buttonPanel = new JPanel(new FlowLayout());
        JButton submitBtn = new JButton("Submit");
        JButton cancelBtn = new JButton("Cancel");

        submitBtn.addActionListener(e -> {
            try {
                LocalDate start = LocalDate.parse(startField.getText().trim());
                LocalDate end = LocalDate.parse(endField.getText().trim());
                String reason = reasonArea.getText().trim();

                if (reason.isEmpty()) {
                    JOptionPane.showMessageDialog(dialog, "Please provide a reason", "Error", JOptionPane.ERROR_MESSAGE);
                    return;
                }

                if (end.isBefore(start)) {
                    JOptionPane.showMessageDialog(dialog, "End date must be after start date", "Error", JOptionPane.ERROR_MESSAGE);
                    return;
                }

                if (manager.addLeaveRequest(userId, start, end, reason)) {
                    JOptionPane.showMessageDialog(dialog, "Leave request submitted successfully!", "Success", JOptionPane.INFORMATION_MESSAGE);
                    loadUserData();
                    dialog.dispose();
                }
            } catch (DateTimeParseException ex) {
                JOptionPane.showMessageDialog(dialog, "Invalid date format. Use YYYY-MM-DD", "Error", JOptionPane.ERROR_MESSAGE);
            }
        });

        cancelBtn.addActionListener(e -> dialog.dispose());

        buttonPanel.add(submitBtn);
        buttonPanel.add(cancelBtn);

        dialog.setLayout(new BorderLayout());
        dialog.add(panel, BorderLayout.CENTER);
        dialog.add(buttonPanel, BorderLayout.SOUTH);
        dialog.setVisible(true);
    }
}

class StaffGUI extends JFrame {
    private static final long serialVersionUID = 1L;
    private StaffManager manager;
    private JTable staffTable, attendanceTable, leaveTable;
    private DefaultTableModel staffModel, attendanceModel, leaveModel;
    private JTextField idField, nameField, positionField, salaryField;
    private JButton addBtn, updBtn, delBtn, searchBtn, approveBtn, rejectBtn;
    private String userRole;

    public StaffGUI(String role) {
        this.userRole = role;
        manager = new StaffManager();
        setupUI();
    }

    private void setupUI() {
        setTitle("Staff Management System - " + userRole);
        setSize(900, 600);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);

        JTabbedPane tabs = new JTabbedPane();
        tabs.add("Staff", createStaffTab());
        tabs.add("All Attendance", createAttendanceTab());
        tabs.add("All Leave Requests", createLeaveTab());

        JButton logoutBtn = new JButton("Logout");
        logoutBtn.addActionListener(e -> {
            this.dispose();
            new LoginSystem();
        });
        
        JPanel bottomPanel = new JPanel(new FlowLayout(FlowLayout.RIGHT));
        bottomPanel.add(logoutBtn);

        JPanel mainPanel = new JPanel(new BorderLayout());
        mainPanel.add(tabs, BorderLayout.CENTER);
        mainPanel.add(bottomPanel, BorderLayout.SOUTH);
        
        add(mainPanel);
        refreshTables();
        applyRoleRestrictions();
    }

    private JPanel createStaffTab() {
        JPanel panel = new JPanel(new BorderLayout());

        staffModel = new DefaultTableModel(new String[]{"ID", "Name", "Position", "Salary"}, 0) {
            private static final long serialVersionUID = 1L;
            @Override
            public boolean isCellEditable(int row, int column) {
                return false;
            }
        };
        staffTable = new JTable(staffModel);
        panel.add(new JScrollPane(staffTable), BorderLayout.CENTER);

        JPanel bottom = new JPanel(new FlowLayout());
        idField = new JTextField(5);
        nameField = new JTextField(10);
        positionField = new JTextField(10);
        salaryField = new JTextField(10);

        bottom.add(new JLabel("ID:")); bottom.add(idField);
        bottom.add(new JLabel("Name:")); bottom.add(nameField);
        bottom.add(new JLabel("Position:")); bottom.add(positionField);
        bottom.add(new JLabel("Salary:")); bottom.add(salaryField);

        addBtn = new JButton("Add");
        updBtn = new JButton("Update");
        delBtn = new JButton("Delete");
        searchBtn = new JButton("Search");

        addBtn.addActionListener(e -> addStaff());
        updBtn.addActionListener(e -> updateStaff());
        delBtn.addActionListener(e -> deleteStaff());
        searchBtn.addActionListener(e -> searchStaff());

        bottom.add(addBtn);
        bottom.add(updBtn);
        bottom.add(delBtn);
        bottom.add(searchBtn);

        panel.add(bottom, BorderLayout.SOUTH);
        return panel;
    }

    private JPanel createAttendanceTab() {
        JPanel panel = new JPanel(new BorderLayout());

        attendanceModel = new DefaultTableModel(new String[]{"Staff ID", "Name", "Date", "Status"}, 0) {
            private static final long serialVersionUID = 1L;
            @Override
            public boolean isCellEditable(int row, int column) {
                return false;
            }
        };
        attendanceTable = new JTable(attendanceModel);
        panel.add(new JScrollPane(attendanceTable), BorderLayout.CENTER);

        return panel;
    }

    private JPanel createLeaveTab() {
        JPanel panel = new JPanel(new BorderLayout());

        leaveModel = new DefaultTableModel(new String[]{"Staff ID", "Name", "Start Date", "End Date", "Reason", "Status"}, 0) {
            private static final long serialVersionUID = 1L;
            @Override
            public boolean isCellEditable(int row, int column) {
                return false;
            }
        };
        leaveTable = new JTable(leaveModel);
        panel.add(new JScrollPane(leaveTable), BorderLayout.CENTER);

        JPanel bottom = new JPanel(new FlowLayout());
        approveBtn = new JButton("Approve Selected");
        rejectBtn = new JButton("Reject Selected");

        approveBtn.addActionListener(e -> approveLeaveRequest());
        rejectBtn.addActionListener(e -> rejectLeaveRequest());

        bottom.add(approveBtn);
        bottom.add(rejectBtn);
        panel.add(bottom, BorderLayout.SOUTH);

        return panel;
    }

    private void applyRoleRestrictions() {
        if (userRole.equals("admin")) {
            updBtn.setEnabled(true);
            delBtn.setEnabled(true);
            approveBtn.setEnabled(false);
            rejectBtn.setEnabled(false);
            salaryField.setEditable(false);
        }
    }

    private void addStaff() {
        if (!userRole.equals("super admin") && !userRole.equals("admin")) return;
        try {
            String name = nameField.getText().trim();
            String position = positionField.getText().trim();
            String salaryText = salaryField.getText().trim();
            
            if (name.isEmpty() || position.isEmpty() || salaryText.isEmpty()) {
                JOptionPane.showMessageDialog(this, "Please fill all fields");
                return;
            }
            
            manager.addStaff(name, position, Double.parseDouble(salaryText));
            refreshTables();
            clearFields();
            JOptionPane.showMessageDialog(this, "Staff added successfully!");
        } catch (NumberFormatException ex) {
            JOptionPane.showMessageDialog(this, "Invalid salary format");
        }
    }

    private void updateStaff() {
        if (!userRole.equals("super admin") && !userRole.equals("admin")) return;
        try {
            int id = Integer.parseInt(idField.getText());
            String name = nameField.getText().trim();
            String position = positionField.getText().trim();
            
            Staff existingStaff = manager.findStaffById(id);
            if (existingStaff == null) {
                JOptionPane.showMessageDialog(this, "Staff ID not found");
                return;
            }
            
            double salary = userRole.equals("admin") ? existingStaff.getSalary() : Double.parseDouble(salaryField.getText());
            if (manager.updateStaff(id, name, position, salary)) {
                refreshTables();
                clearFields();
                JOptionPane.showMessageDialog(this, "Staff updated successfully!");
            }
        } catch (NumberFormatException ex) {
            JOptionPane.showMessageDialog(this, "Invalid ID or salary format");
        }
    }

    private void deleteStaff() {
        if (!userRole.equals("super admin") && !userRole.equals("admin")) return;
        try {
            int id = Integer.parseInt(idField.getText());
            int confirm = JOptionPane.showConfirmDialog(this, "Are you sure you want to delete this staff member?", "Confirm Delete", JOptionPane.YES_NO_OPTION);
            if (confirm == JOptionPane.YES_OPTION) {
                if (manager.deleteStaff(id)) {
                    refreshTables();
                    clearFields();
                    JOptionPane.showMessageDialog(this, "Staff deleted successfully!");
                } else {
                    JOptionPane.showMessageDialog(this, "Staff ID not found");
                }
            }
        } catch (NumberFormatException ex) {
            JOptionPane.showMessageDialog(this, "Invalid ID format");
        }
    }

    private void searchStaff() {
        try {
            int id = Integer.parseInt(idField.getText());
            Staff s = manager.findStaffById(id);
            if (s != null) {
                nameField.setText(s.getName());
                positionField.setText(s.getPosition());
                salaryField.setText(String.valueOf(s.getSalary()));
            } else {
                JOptionPane.showMessageDialog(this, "Staff not found");
            }
        } catch (NumberFormatException ex) {
            JOptionPane.showMessageDialog(this, "Invalid ID format");
        }
    }

    private void approveLeaveRequest() {
        if (!userRole.equals("super admin")) return;
        int selectedRow = leaveTable.getSelectedRow();
        if (selectedRow == -1) {
            JOptionPane.showMessageDialog(this, "Please select a leave request");
            return;
        }
        
        try {
            int staffId = (int) leaveModel.getValueAt(selectedRow, 0);
            Staff staff = manager.findStaffById(staffId);
            if (staff == null) {
                JOptionPane.showMessageDialog(this, "Staff not found");
                return;
            }
            int requestIndex = getLeaveRequestIndex(staff, selectedRow);
            
            if (requestIndex == -1) {
                JOptionPane.showMessageDialog(this, "Leave request not found");
                return;
            }
            
            if (manager.approveLeaveRequest(staffId, requestIndex)) {
                refreshTables();
                JOptionPane.showMessageDialog(this, "Leave request approved!");
            }
        } catch (Exception ex) {
            JOptionPane.showMessageDialog(this, "Approval failed: " + ex.getMessage());
        }
    }

    private void rejectLeaveRequest() {
        if (!userRole.equals("super admin")) return;
        int selectedRow = leaveTable.getSelectedRow();
        if (selectedRow == -1) {
            JOptionPane.showMessageDialog(this, "Please select a leave request");
            return;
        }
        
        try {
            int staffId = (int) leaveModel.getValueAt(selectedRow, 0);
            Staff staff = manager.findStaffById(staffId);
            if (staff == null) {
                JOptionPane.showMessageDialog(this, "Staff not found");
                return;
            }
            int requestIndex = getLeaveRequestIndex(staff, selectedRow);
            
            if (requestIndex == -1) {
                JOptionPane.showMessageDialog(this, "Leave request not found");
                return;
            }
            
            if (manager.rejectLeaveRequest(staffId, requestIndex)) {
                refreshTables();
                JOptionPane.showMessageDialog(this, "Leave request rejected!");
            }
        } catch (Exception ex) {
            JOptionPane.showMessageDialog(this, "Rejection failed: " + ex.getMessage());
        }
    }

    private int getLeaveRequestIndex(Staff staff, int tableRow) {
        LocalDate startDate = (LocalDate) leaveModel.getValueAt(tableRow, 2);
        LocalDate endDate = (LocalDate) leaveModel.getValueAt(tableRow, 3);
        
        List<LeaveRequest> requests = staff.getLeaveRequests();
        for (int i = 0; i < requests.size(); i++) {
            LeaveRequest req = requests.get(i);
            if (req.getStartDate().equals(startDate) && req.getEndDate().equals(endDate)) {
                return i;
            }
        }
        return -1;
    }

    private void refreshTables() {
        staffModel.setRowCount(0);
        attendanceModel.setRowCount(0);
        leaveModel.setRowCount(0);

        for (Staff s : manager.getAllStaff()) {
            staffModel.addRow(new Object[]{s.getId(), s.getName(), s.getPosition(), s.getSalary()});
            
            s.getAttendance().forEach((date, status) ->
                    attendanceModel.addRow(new Object[]{s.getId(), s.getName(), date, status}));
            
            for (LeaveRequest req : s.getLeaveRequests()) {
                leaveModel.addRow(new Object[]{
                        s.getId(), s.getName(),
                        req.getStartDate(), req.getEndDate(), req.getReason(), req.getStatus()});
            }
        }
    }

    private void clearFields() {
        idField.setText("");
        nameField.setText("");
        positionField.setText("");
        salaryField.setText("");
    }
}

public class StaffManagementSystem {
    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            try {
                UIManager.setLookAndFeel(UIManager.getSystemLookAndFeelClassName());
            } catch (Exception e) {
                // Use default look and feel
            }
            new LoginSystem();
        });
    }
}
