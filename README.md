import javax.swing.*;
import java.awt.*;
import java.awt.geom.GeneralPath;
import java.util.Random;

public class CoraçãoBrilhante extends JFrame {

    public CoraçãoBrilhante() {
        setTitle("Coração Brilhante");
        setSize(400, 400);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
        add(new PainelCoração());
    }

    private class PainelCoração extends JPanel implements Runnable {
        private float brilho = 0.5f;
        private int direção = 1;
        private final Random random = new Random();

        public PainelCoração() {
            setBackground(Color.BLACK);
            new Thread(this).start();
        }

        @Override
        protected void paintComponent(Graphics g) {
            super.paintComponent(g);
            Graphics2D g2d = (Graphics2D) g;
            g2d.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);

            // Calcula cor com base no brilho
            int vermelho = (int) (255 * (0.8f + 0.2f * brilho));
            int verde = (int) (50 * brilho);
            int azul = (int) (80 * brilho);
            g2d.setColor(new Color(vermelho, verde, azul));

            // Desenha coração usando GeneralPath
            GeneralPath coração = new GeneralPath();
            int centroX = getWidth() / 2;
            int centroY = getHeight() / 2;
            int tamanho = 100;

            coração.moveTo(centroX, centroY - tamanho / 2);
            coração.curveTo(
                centroX + tamanho / 2, centroY - tamanho,
                centroX + tamanho, centroY + tamanho / 4,
                centroX, centroY + tamanho / 2
            );
            coração.curveTo(
                centroX - tamanho, centroY + tamanho / 4,
                centroX - tamanho / 2, centroY - tamanho,
                centroX, centroY - tamanho / 2
            );
            coração.closePath();

            g2d.fill(coração);

            // Adiciona pontos de brilho aleatórios
            g2d.setColor(Color.WHITE);
            for (int i = 0; i < 20; i++) {
                int x = centroX - tamanho / 2 + random.nextInt(tamanho);
                int y = centroY - tamanho / 2 + random.nextInt(tamanho);
                if (coração.contains(x, y)) {
                    int tamanhoPonto = random.nextInt(3) + 1;
                    g2d.fillOval(x, y, tamanhoPonto, tamanhoPonto);
                }
            }
        }

        @Override
        public void run() {
            while (true) {
                // Altera brilho entre 0.2 e 1.0
                brilho += direção * 0.01f;
                if (brilho >= 1.0f) direção = -1;
                if (brilho <= 0.2f) direção = 1;
                repaint();
                try {
                    Thread.sleep(50);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> new CoraçãoBrilhante().setVisible(true));
    }
}
