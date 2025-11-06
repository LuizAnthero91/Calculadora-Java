# Calculadora-Java com operações básicas e com gráfico.
<img width="300" height="480" alt="image" src="https://github.com/user-attachments/assets/c5206c63-5c09-4375-813a-7feaf6019520" />
# Calculadora-Java
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class Calculadora extends JFrame implements ActionListener {

    // Componentes da Interface
    private final JTextField campoDisplay; // Onde os números e resultados são exibidos
    private final JPanel painelBotoes;    // Painel para organizar os botões

    // Variáveis de Lógica da Calculadora
    private double numero1 = 0;
    private String operador = null;
    private boolean novoNumero = true; // Flag para indicar se o próximo dígito deve limpar o display

    // Rótulos dos botões (Disposição: 4 linhas x 4 colunas)
    private final String[] rotulosBotoes = {
            "7", "8", "9", "/",
            "4", "5", "6", "*",
            "1", "2", "3", "-",
            "0", ".", "=", "+"
    };

    /**
     * Construtor da Calculadora
     */
    public Calculadora() {
        super("Calculadora Simples em Java"); // Título da janela

        // 1. Configurar a Janela (JFrame)
        this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        this.setSize(300, 400); // Tamanho da janela
        this.setLayout(new BorderLayout());

        // 2. Criar e Configurar o Display (JTextField)
        campoDisplay = new JTextField("0");
        campoDisplay.setEditable(false); // O usuário não edita o display diretamente
        campoDisplay.setFont(new Font("Arial", Font.BOLD, 30));
        campoDisplay.setHorizontalAlignment(SwingConstants.RIGHT); // Alinha o texto à direita
        this.add(campoDisplay, BorderLayout.NORTH);

        // 3. Criar e Configurar o Painel de Botões (JPanel)
        painelBotoes = new JPanel();
        painelBotoes.setLayout(new GridLayout(4, 4, 5, 5)); // 4 linhas, 4 colunas, com espaçamento de 5px

        // 4. Criar e Adicionar os Botões
        for (String rotulo : rotulosBotoes) {
            JButton botao = new JButton(rotulo);
            botao.setFont(new Font("Arial", Font.PLAIN, 20));
            botao.addActionListener(this); // O próprio objeto Calculadora tratará os cliques
            painelBotoes.add(botao);
        }

        // Adicionar um botão de limpar 'C'
        JButton btnLimpar = new JButton("C");
        btnLimpar.setFont(new Font("Arial", Font.BOLD, 20));
        btnLimpar.setBackground(Color.RED);
        btnLimpar.setForeground(Color.WHITE);
        btnLimpar.addActionListener(this);

        // Adicionar o botão 'C' e o painel de botões
        JPanel painelExtra = new JPanel(new BorderLayout());
        painelExtra.add(btnLimpar, BorderLayout.NORTH);
        painelExtra.add(painelBotoes, BorderLayout.CENTER);

        this.add(painelExtra, BorderLayout.CENTER);

        this.setVisible(true); // Tornar a janela visível
    }

    /**
     * Lógica para lidar com os cliques dos botões (ActionListener)
     */
    @Override
    public void actionPerformed(ActionEvent e) {
        String comando = e.getActionCommand();

        // 1. Se for um número (0-9) ou ponto (.)
        if (comando.matches("[0-9]") || comando.equals(".")) {
            if (novoNumero) {
                campoDisplay.setText(comando.equals(".") ? "0." : comando);
                novoNumero = false;
            } else if (comando.equals(".")) {
                if (!campoDisplay.getText().contains(".")) {
                    campoDisplay.setText(campoDisplay.getText() + ".");
                }
            } else {
                campoDisplay.setText(campoDisplay.getText() + comando);
            }
        }
        // 2. Se for um operador (+, -, *, /)
        else if (comando.matches("[+\\-*/]")) {
            if (operador != null) {
                // Se já houver um operador, calcula o resultado da operação anterior
                calcularResultado();
            }
            numero1 = Double.parseDouble(campoDisplay.getText());
            operador = comando;
            novoNumero = true;
        }
        // 3. Se for o botão Igual (=)
        else if (comando.equals("=")) {
            if (operador != null) {
                calcularResultado();
                operador = null;
                novoNumero = true;
            }
        }
        // 4. Se for o botão Limpar (C)
        else if (comando.equals("C")) {
            campoDisplay.setText("0");
            numero1 = 0;
            operador = null;
            novoNumero = true;
        }
    }

    /**
     * Executa o cálculo da operação pendente
     */
    private void calcularResultado() {
        try {
            double numero2 = Double.parseDouble(campoDisplay.getText());
            double resultado = 0;

            switch (operador) {
                case "+":
                    resultado = numero1 + numero2;
                    break;
                case "-":
                    resultado = numero1 - numero2;
                    break;
                case "*":
                    resultado = numero1 * numero2;
                    break;
                case "/":
                    if (numero2 == 0) {
                        campoDisplay.setText("Erro: Divisão por 0");
                        return;
                    }
                    resultado = numero1 / numero2;
                    break;
            }

            // Exibir o resultado, removendo o ".0" se for um número inteiro
            if (resultado == (long) resultado) {
                campoDisplay.setText(String.format("%d", (long) resultado));
            } else {
                campoDisplay.setText(String.format("%.2f", resultado));
            }

            numero1 = resultado; // O resultado se torna o primeiro número para a próxima operação

        } catch (NumberFormatException ex) {
            campoDisplay.setText("Erro de formato");
        }
    }

    /**
     * Método principal para iniciar a aplicação
     */
    public static void main(String[] args) {
        // Garante que a interface seja iniciada na Thread de Eventos (recomendado pelo Swing)
        SwingUtilities.invokeLater(Calculadora::new);
    }
}




