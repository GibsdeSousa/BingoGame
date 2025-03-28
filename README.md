import javax.swing.*;
import javax.swing.table.DefaultTableCellRenderer;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class BingoGame {
    private JFrame frame;
    private JTable table;
    private DefaultTableModel model;
    private List<Integer> numbers;
    private Timer blinkTimer;
    private int lastNumber = -1;
    private boolean isBlinking = false;
    
    public BingoGame() {
        frame = new JFrame("Bingo Game");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(400, 500);
        
        model = new DefaultTableModel(16, 5);
        table = new JTable(model);
        
        String[] headers = {"B", "I", "N", "G", "O"};
        for (int i = 0; i < headers.length; i++) {
            model.setValueAt(headers[i], 0, i);
        }
        
        table.setRowHeight(30);
        table.setEnabled(false);
        
        DefaultTableCellRenderer headerRenderer = new DefaultTableCellRenderer();
        headerRenderer.setHorizontalAlignment(SwingConstants.CENTER);
        headerRenderer.setFont(new Font("Arial", Font.BOLD, 24));
        
        DefaultTableCellRenderer numberRenderer = new DefaultTableCellRenderer();
        numberRenderer.setHorizontalAlignment(SwingConstants.CENTER);
        numberRenderer.setFont(new Font("Arial Black", Font.PLAIN, 20));
        numberRenderer.setForeground(Color.BLUE);
        
        for (int i = 0; i < table.getColumnCount(); i++) {
            table.getColumnModel().getColumn(i).setHeaderRenderer(headerRenderer);
            table.getColumnModel().getColumn(i).setCellRenderer(numberRenderer);
        }
        
        frame.add(new JScrollPane(table), BorderLayout.CENTER);
        
        JPanel panel = new JPanel();
        JButton sortearBtn = new JButton("SORTEAR");
        JButton reiniciarBtn = new JButton("REINICIAR");
        JButton sairBtn = new JButton("SAIR DO JOGO");
        
        panel.add(sortearBtn);
        panel.add(reiniciarBtn);
        panel.add(sairBtn);
        frame.add(panel, BorderLayout.SOUTH);
        
        sortearBtn.addActionListener(e -> sortearNumero());
        reiniciarBtn.addActionListener(e -> reiniciarJogo());
        sairBtn.addActionListener(e -> System.exit(0));
        
        iniciarNumeros();
        frame.setVisible(true);
    }
    
    private void iniciarNumeros() {
        numbers = new ArrayList<>();
        for (int i = 1; i <= 75; i++) {
            numbers.add(i);
        }
        Collections.shuffle(numbers);
    }
    
    private void sortearNumero() {
        if (numbers.isEmpty()) {
            JOptionPane.showMessageDialog(frame, "Todos os números foram sorteados!");
            return;
        }
        
        if (isBlinking && lastNumber != -1) {
            piscarNumero(lastNumber, false);
        }
        
        lastNumber = numbers.remove(0);
        piscarNumero(lastNumber, true);
    }
    
    private void piscarNumero(int number, boolean start) {
        int column = (number - 1) / 15;
        int row = (number - 1) % 15 + 1;
        
        if (start) {
            isBlinking = true;
            blinkTimer = new Timer(500, new ActionListener() {
                boolean visible = true;
                @Override
                public void actionPerformed(ActionEvent e) {
                    model.setValueAt(visible ? number : "", row, column);
                    visible = !visible;
                }
            });
            blinkTimer.start();
        } else {
            if (blinkTimer != null) {
                blinkTimer.stop();
            }
            isBlinking = false;
            model.setValueAt(number, row, column);
        }
    }
    
    private void reiniciarJogo() {
        if (blinkTimer != null) {
            blinkTimer.stop();
        }
        
        model.setRowCount(0);
        model.setRowCount(16);
        
        String[] headers = {"B", "I", "N", "G", "O"};
        for (int i = 0; i < headers.length; i++) {
            model.setValueAt(headers[i], 0, i);
        }
        
        iniciarNumeros();
        lastNumber = -1;
    }
    
    public static void main(String[] args) {
        SwingUtilities.invokeLater(BingoGame::new);
    }
}
