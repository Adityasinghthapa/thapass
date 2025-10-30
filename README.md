# thapass
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.*;

class Train {
    int no;
    String name;
    double fare;

    Train(int no, String name, double fare) {
        this.no = no;
        this.name = name;
        this.fare = fare;
    }

    public String toString() {
        return no + " - " + name + " (Rs." + fare + ")";
    }
}

class Booking {
    int id;
    String passengerName;
    int age;
    Train train;

    Booking(int id, String name, int age, Train train) {
        this.id = id;
        this.passengerName = name;
        this.age = age;
        this.train = train;
    }
}

public class SimpleRailwaySystem extends JFrame {
    ArrayList<Train> trains = new ArrayList<>();
    ArrayList<Booking> bookings = new ArrayList<>();
    int bookingCounter = 1;

    JComboBox<Train> trainBox;
    JTextField nameField, ageField, cancelField;
    JTextArea outputArea;

    public SimpleRailwaySystem() {
        setTitle("Simple Railway Reservation");
        setSize(500, 400);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
        setLayout(new BorderLayout());
        trains.add(new Train(101, "Rajdhani Express", 1200));
        trains.add(new Train(102, "Shatabdi Express", 800));
        trains.add(new Train(103, "Garib Rath", 600));

        // ----- Booking panel -----
        JPanel top = new JPanel(new GridLayout(4, 2, 5, 5));
        top.setBorder(BorderFactory.createTitledBorder("Book Ticket"));

        top.add(new JLabel("Name:"));
        nameField = new JTextField();
        top.add(nameField);

        top.add(new JLabel("Age:"));
        ageField = new JTextField();
        top.add(ageField);

        top.add(new JLabel("Select Train:"));
        trainBox = new JComboBox<>(trains.toArray(new Train[0]));
        top.add(trainBox);

        JButton bookBtn = new JButton("Book Ticket");
        top.add(new JLabel());
        top.add(bookBtn);

        // ----- Output area -----
        outputArea = new JTextArea();
        outputArea.setEditable(false);
        outputArea.setBorder(BorderFactory.createTitledBorder("Bookings"));

        // ----- Cancel panel -----
        JPanel bottom = new JPanel();
        bottom.add(new JLabel("Cancel Booking ID:"));
        cancelField = new JTextField(5);
        bottom.add(cancelField);
        JButton cancelBtn = new JButton("Cancel");
        bottom.add(cancelBtn);

        // ----- Add to frame -----
        add(top, BorderLayout.NORTH);
        add(new JScrollPane(outputArea), BorderLayout.CENTER);
        add(bottom, BorderLayout.SOUTH);

        // ----- Button actions -----
        bookBtn.addActionListener(e -> bookTicket());
        cancelBtn.addActionListener(e -> cancelBooking());
    }

    void bookTicket() {
        String name = nameField.getText().trim();
        String ageText = ageField.getText().trim();
        if (name.isEmpty() || ageText.isEmpty()) {
            JOptionPane.showMessageDialog(this, "Please fill all details!");
            return;
        }

        int age;
        try {
            age = Integer.parseInt(ageText);
        } catch (NumberFormatException e) {
            JOptionPane.showMessageDialog(this, "Invalid age!");
            return;
        }

        Train selectedTrain = (Train) trainBox.getSelectedItem();
        Booking b = new Booking(bookingCounter++, name, age, selectedTrain);
        bookings.add(b);
        showBookings();

        JOptionPane.showMessageDialog(this, "Booking Confirmed!\nID: " + b.id);
        nameField.setText("");
        ageField.setText("");
    }

    void cancelBooking() {
        String idText = cancelField.getText().trim();
        if (idText.isEmpty()) {
            JOptionPane.showMessageDialog(this, "Enter Booking ID!");
            return;
        }

        int id;
        try {
            id = Integer.parseInt(idText);
        } catch (NumberFormatException e) {
            JOptionPane.showMessageDialog(this, "Invalid ID!");
            return;
        }

        Booking found = null;
        for (Booking b : bookings)
            if (b.id == id) found = b;

        if (found != null) {
            bookings.remove(found);
            JOptionPane.showMessageDialog(this, "Booking Cancelled!");
            showBookings();
        } else {
            JOptionPane.showMessageDialog(this, "ID Not Found!");
        }

        cancelField.setText("");
    }

    void showBookings() {
        outputArea.setText("");
        for (Booking b : bookings) {
            outputArea.append("ID: " + b.id + " | " + b.passengerName + " (" + b.age + " yrs) | " + b.train.name + "\n");
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> new SimpleRailwaySystem().setVisible(true));
    }
}
