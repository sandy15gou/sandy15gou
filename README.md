Berikut adalah komentar mengenai fungsionalitas kode `TicTacToeGui`:

```java
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;

public class TicTacToeGui extends JFrame implements ActionListener {
    // Variabel untuk menyimpan skor pemain X dan O, serta jumlah langkah yang dilakukan
    private int xScore, oScore, moveCounter;

    // Flag untuk menandai giliran pemain (pemain X atau O)
    private boolean isPlayerOne;

    // Label untuk menampilkan giliran, skor, dan hasil pertandingan
    private JLabel turnLabel, scoreLabel, resultLabel;
    private JButton[][] board; // Matriks untuk menyimpan tombol-tombol papan Tic Tac Toe
    private JDialog resultDialog; // Dialog yang muncul untuk menampilkan hasil pertandingan

    public TicTacToeGui(){
        super("Tic Tac Toe (Java Swing)"); // Menginisialisasi JFrame dengan judul
        setSize(CommonConstants.FRAME_SIZE); // Mengatur ukuran jendela
        setDefaultCloseOperation(EXIT_ON_CLOSE); // Menutup aplikasi saat jendela ditutup
        setResizable(false); // Tidak memungkinkan ukuran jendela untuk diubah
        setLocationRelativeTo(null); // Menampilkan jendela di tengah layar
        setLayout(null); // Tidak menggunakan layout manager
        getContentPane().setBackground(CommonConstants.BACKGROUND_COLOR); // Mengatur warna latar belakang

        // Inisialisasi variabel dan dialog hasil
        createResultDialog();
        board = new JButton[3][3]; // Membuat papan Tic Tac Toe berukuran 3x3

        // Pemain X selalu mulai terlebih dahulu
        isPlayerOne = true;

        // Memanggil metode untuk menambahkan komponen GUI
        addGuiComponent();
    }

    private void addGuiComponent(){
        // Membuat bar label di atas GUI
        JLabel barLabel = new JLabel();
        barLabel.setOpaque(true);
        barLabel.setBackground(CommonConstants.BAR_COLOR); // Warna bar
        barLabel.setBounds(0, 0, CommonConstants.FRAME_SIZE.width, 25); // Mengatur posisi dan ukuran

        // Label untuk menunjukkan giliran pemain
        turnLabel = new JLabel(CommonConstants.X_LABEL); // Menampilkan "X" di awal
        turnLabel.setHorizontalAlignment(SwingConstants.CENTER); // Teks berada di tengah
        turnLabel.setFont(new Font("Dialog", Font.PLAIN, 40)); // Mengatur font
        turnLabel.setOpaque(true); // Label mendukung latar belakang
        turnLabel.setBackground(CommonConstants.X_COLOR); // Warna latar belakang label giliran
        turnLabel.setForeground(CommonConstants.BOARD_COLOR); // Warna teks
        turnLabel.setBounds(
                (CommonConstants.FRAME_SIZE.width - turnLabel.getPreferredSize().width)/2,
                0,
                turnLabel.getPreferredSize().width,
                turnLabel.getPreferredSize().height
        );

        // Label untuk menampilkan skor
        scoreLabel = new JLabel(CommonConstants.SCORE_LABEL); // Menampilkan label skor
        scoreLabel.setFont(new Font("Dialog", Font.PLAIN, 40)); // Mengatur font
        scoreLabel.setHorizontalAlignment(SwingConstants.CENTER); // Posisi teks di tengah
        scoreLabel.setForeground(CommonConstants.BOARD_COLOR); // Warna teks
        scoreLabel.setBounds(0,
                turnLabel.getY() + turnLabel.getPreferredSize().height + 25,
                CommonConstants.FRAME_SIZE.width,
                scoreLabel.getPreferredSize().height
        );

        // Papan Tic Tac Toe menggunakan GridLayout 3x3
        GridLayout gridLayout = new GridLayout(3, 3);
        JPanel boardPanel = new JPanel(gridLayout); // Panel papan
        boardPanel.setBounds(0,
                scoreLabel.getY() + scoreLabel.getPreferredSize().height + 35,
                CommonConstants.BOARD_SIZE.width,
                CommonConstants.BOARD_SIZE.height
        );

        // Membuat papan permainan dengan tombol
        for(int i = 0; i < board.length; i++){
            for(int j = 0; j < board[i].length; j++){
                JButton button = new JButton();
                button.setFont(new Font("Dialog", Font.PLAIN, 180)); // Font besar untuk X dan O
                button.setPreferredSize(CommonConstants.BUTTON_SIZE); // Ukuran tombol
                button.setBackground(CommonConstants.BACKGROUND_COLOR); // Warna latar belakang tombol
                button.addActionListener(this); // Menambahkan action listener untuk tombol
                button.setBorder(BorderFactory.createLineBorder(CommonConstants.BOARD_COLOR)); // Membuat border pada tombol

                // Menambahkan tombol ke matriks papan
                board[i][j] = button;
                boardPanel.add(board[i][j]);
            }
        }

        // Tombol reset untuk memulai ulang permainan
        JButton resetButton = new JButton("Reset");
        resetButton.setFont(new Font("Dialog", Font.PLAIN, 24)); // Mengatur font tombol reset
        resetButton.addActionListener(this); // Menambahkan action listener
        resetButton.setBackground(CommonConstants.BOARD_COLOR); // Warna tombol
        resetButton.setBounds(
                (CommonConstants.FRAME_SIZE.width - resetButton.getPreferredSize().width)/2,
                CommonConstants.FRAME_SIZE.height - 100,
                resetButton.getPreferredSize().width,
                resetButton.getPreferredSize().height
        );

        // Menambahkan semua komponen ke JFrame
        getContentPane().add(turnLabel);
        getContentPane().add(barLabel);
        getContentPane().add(scoreLabel);
        getContentPane().add(boardPanel);
        getContentPane().add(resetButton);
    }

    // Metode untuk membuat dialog hasil pertandingan
    private void createResultDialog(){
        resultDialog = new JDialog();
        resultDialog.getContentPane().setBackground(CommonConstants.BACKGROUND_COLOR); // Warna latar belakang
        resultDialog.setResizable(false); // Ukuran dialog tidak bisa diubah
        resultDialog.setTitle("Result"); // Judul dialog
        resultDialog.setSize(CommonConstants.RESULT_DIALOG_SIZE); // Ukuran dialog
        resultDialog.setLocationRelativeTo(this); // Menampilkan dialog di tengah jendela utama
        resultDialog.setModal(true); // Dialog bersifat modal
        resultDialog.setLayout(new GridLayout(2, 1)); // Layout dialog dengan 2 baris
        resultDialog.addWindowListener(new WindowAdapter() {
            @Override
            public void windowClosing(WindowEvent e) {
                resetGame(); // Mengatur permainan ulang saat dialog ditutup
            }
        });

        // Label hasil pertandingan
        resultLabel = new JLabel();
        resultLabel.setFont(new Font("Dialog", Font.BOLD, 18)); // Font tebal untuk hasil
        resultLabel.setForeground(CommonConstants.BOARD_COLOR); // Warna teks
        resultLabel.setHorizontalAlignment(SwingConstants.CENTER); // Teks berada di tengah

        // Tombol untuk memulai pertandingan lagi
        JButton restartButton = new JButton("Play Again");
        restartButton.setBackground(CommonConstants.BOARD_COLOR); // Warna tombol
        restartButton.addActionListener(this); // Menambahkan action listener

        resultDialog.add(resultLabel);
        resultDialog.add(restartButton);
    }

    // Action listener untuk menangani klik pada tombol
    @Override
    public void actionPerformed(ActionEvent e) {
        String command = e.getActionCommand();
        if(command.equals("Reset") || command.equals("Play Again")){
            resetGame(); // Mengatur permainan ulang

            // Jika tombol "Reset" ditekan, skor juga direset
            if(command.equals("Reset"))
                xScore = oScore = 0;

            if(command.equals("Play Again"))
                resultDialog.setVisible(false); // Menyembunyikan dialog hasil
        }else{
            // Jika pemain mengklik tombol di papan
            JButton button = (JButton) e.getSource();
            if(button.getText().equals("")){
                moveCounter++; // Meningkatkan penghitung langkah

                // Menandai tombol dengan X atau O tergantung pada giliran pemain
                if(isPlayerOne){
                    button.setText(CommonConstants.X_LABEL); // Pemain X
                    button.setForeground(CommonConstants.X_COLOR); // Warna X
                    turnLabel.setText(CommonConstants.O_LABEL); // Ganti giliran ke pemain O
                    turnLabel.setBackground(CommonConstants.O_COLOR); // Warna untuk giliran O
                    isPlayerOne = false; // Giliran berpindah ke pemain O
                }else {
                    button.setText(CommonConstants.O_LABEL); // Pemain O
                    button.setForeground(CommonConstants.O_COLOR); // Warna O
                    turnLabel.setText(CommonConstants.X_LABEL); // Ganti giliran ke pemain X
                    turnLabel.setBackground(CommonConstants.X_COLOR); // Warna untuk giliran X
                    isPlayerOne = true; // Giliran berpindah ke pemain X
                }

                // Memeriksa apakah pemain X atau O menang
                if(isPlayerOne){
                    checkOWin(); // Cek jika O menang
                }else{
                    checkXWin(); // Cek jika X menang
                }

                // Memeriksa apakah pertandingan berakhir seri
                checkDraw();

                // Update tampilan skor
                scoreLabel.setText("X: " + xScore + " | O: " + oScore);
            }

            repaint(); // Render ulang tampilan
            revalidate(); // Validasi ulang layout
        }
    }

    // Metode untuk memeriksa apakah pemain X menang
    private void checkXWin
