
import java.util.concurrent.locks.ReentrantLock;
import java.io.File;
import java.io.FileNotFoundException;
import java.io.PrintStream;
import java.util.Scanner;

public class JogoVida {
    
    static int lockCont;
    static int contLive;
    static int nGeracoes;
    static int nThreads;
    static int m;
    static int[][] matrizA;
    static int[][] matrizB;
    static Scanner entrada;
    static ReentrantLock lockContador = new ReentrantLock(true);

    public static void main(String[] args) throws InterruptedException {
        //entrada (.txt + quantidade de geracoes)
        try {
            BooleanoSincronizado SyncB = new BooleanoSincronizado();
            Input(/*args[0]*/"teste.txt");
            nGeracoes = Integer.parseInt(/*args[1]*/"1000");
            LoadMatriz();
            nThreads = Integer.parseInt(/*args[2]*/"2");
            lockCont = nThreads;
            SyncB.setBooleano(true);

            switch (nThreads) {
                case 1:
                    for (int i = 0; i < nGeracoes; i++) {
                        StartGeneration(0, 0, SyncB);
                    }
                    break;
                case 2:
                case 4:
                    Threads t[] = new Threads[nThreads];
                    for (int i = 0; i < nThreads; i++) {
                        t[i] = new Threads(i, nThreads, m, SyncB);
                    }
                    for (int j = 0; j < nThreads; j++) {
                        t[j].start();
                    }
                    for (int j = 0; j < nThreads; j++) {
                        t[j].join();
                    }
                    break;
                default:
                    System.out.println("**ERRO: Nao ha implementacao para essa quantidade de Threads!!**");
                    break;
            }
            Write();

        } catch (FileNotFoundException e) {
            System.out.println("Arquivo nao encontrado, favor tentar novamente :)");
        }
    }

    static void Input(String txt) throws FileNotFoundException {
        entrada = new Scanner(new File(txt));

        m = Integer.parseInt(entrada.nextLine());

        matrizA = new int[m][m];
        matrizB = new int[m][m];
        contLive = 0;
    }

    private static void LoadMatriz() {
        String[] temp = new String[matrizA.length];

        for (int i = 0; i < matrizA.length; i++) {
            temp[i] = entrada.nextLine();
            for (int j = 0; j < matrizA[i].length; j++) {
                matrizA[i][j] = Character.getNumericValue(temp[i].charAt(j));
            }
        }
    }

    protected static void StartGeneration(int n, int id, BooleanoSincronizado SyncB) {
        int l;
        if (nThreads == 1) {
            SyncB.setBooleano(true);
            l = m;
        } else {
            l = n * (id + 1);
            n = n * id;
        }
        for (int i = n; i < l; i++) {
            for (int j = 0; j < m; j++) {
                //Primeira linha
                if (i == 0) {
                    //PrimeiraLinha && primeira coluna
                    if (j == 0) {
                        //direita
                        if (matrizA[i][j + 1] == 1) {
                            contLive++;
                        }
                        //direita baixo
                        if (matrizA[i + 1][j + 1] == 1) {
                            contLive++;
                        }
                        //baixo
                        if (matrizA[i + 1][j] == 1) {
                            contLive++;
                        }
                        //Primeira Linha && ultima Coluna
                    } else if (j == m - 1) {
                        //esquerda
                        if (matrizA[i][j - 1] == 1) {
                            contLive++;
                        }
                        //esquerda Baixo
                        if (matrizA[i + 1][j - 1] == 1) {
                            contLive++;
                        }
                        //baixo
                        if (matrizA[i + 1][j] == 1) {
                            contLive++;
                        }
                        //primeira linha !primeiraColuna && !ultimaColuna
                    } else {
                        //esquerda
                        if (matrizA[i][j - 1] == 1) {
                            contLive++;
                        }
                        //direita
                        if (matrizA[i][j + 1] == 1) {
                            contLive++;
                        }
                        //BaixoEsquerda
                        if (matrizA[i + 1][j - 1] == 1) {
                            contLive++;
                        }
                        //Baixo
                        if (matrizA[i + 1][j] == 1) {
                            contLive++;
                        }
                        //BaixoDireita
                        if (matrizA[i + 1][j + 1] == 1) {
                            contLive++;
                        }
                    }
                    //ultima linha
                } else if (i == m - 1) {
                    //UltimaLinha && Primeira coluna
                    if (j == 0) {
                        //cima
                        if (matrizA[i - 1][j] == 1) {
                            contLive++;
                        }
                        //cimaDireita
                        if (matrizA[i - 1][j + 1] == 1) {
                            contLive++;
                        }
                        //Direita
                        if (matrizA[i][j + 1] == 1) {
                            contLive++;
                        }
                        //UltimaLinha && ultima coluna
                    } else if (j == m - 1) {
                        //cima
                        if (matrizA[i - 1][j] == 1) {
                            contLive++;
                        }
                        //cimaEsquerda
                        if (matrizA[i - 1][j - 1] == 1) {
                            contLive++;
                        }
                        //esquerda
                        if (matrizA[i][j - 1] == 1) {
                            contLive++;
                        }
                    } //ultima linha && !primeiraColuna && !ultimaColuna
                    else {
                        //Esquerda
                        if (matrizA[i][j - 1] == 1) {
                            contLive++;
                        }
                        //Direita
                        if (matrizA[i][j + 1] == 1) {
                            contLive++;
                        }
                        //Cima
                        if (matrizA[i - 1][j] == 1) {
                            contLive++;
                        }
                        //CimaEsquerda
                        if (matrizA[i - 1][j - 1] == 1) {
                            contLive++;
                        }
                        //CimaDireita
                        if (matrizA[i - 1][j + 1] == 1) {
                            contLive++;
                        }
                    }
                } //linhas do meio, aqui teram 3 formas diferente j == 0, j == m - 1, outros
                else //meio && primeiraColuna
                {
                    if (j == 0) {
                        //cima
                        if (matrizA[i - 1][j] == 1) {
                            contLive++;
                        }
                        //cimaDireita
                        if (matrizA[i - 1][j + 1] == 1) {
                            contLive++;
                        }
                        //Direita
                        if (matrizA[i][j + 1] == 1) {
                            contLive++;
                        }
                        //Baixo
                        if (matrizA[i + 1][j] == 1) {
                            contLive++;
                        }
                        //BaixoDireita
                        if (matrizA[i + 1][j + 1] == 1) {
                            contLive++;
                        }
                    } //Meio && Ultima Coluna
                    else if (j == m - 1) {
                        //cima
                        if (matrizA[i - 1][j] == 1) {
                            contLive++;
                        }
                        //cimaEsquerda
                        if (matrizA[i - 1][j - 1] == 1) {
                            contLive++;
                        }
                        //Esquerda
                        if (matrizA[i][j - 1] == 1) {
                            contLive++;
                        }
                        //Baixo
                        if (matrizA[i + 1][j] == 1) {
                            contLive++;
                        }
                        //BaixoEsquerda
                        if (matrizA[i + 1][j - 1] == 1) {
                            contLive++;
                        }
                    } //Meio && !primeiraColuna && !ultimaColuna
                    else {
                        //Cima
                        if (matrizA[i - 1][j] == 1) {
                            contLive++;
                        }
                        //CimaEsquerda
                        if (matrizA[i - 1][j - 1] == 1) {
                            contLive++;
                        }
                        //CimaDireita
                        if (matrizA[i - 1][j + 1] == 1) {
                            contLive++;
                        }
                        //Esquerda
                        if (matrizA[i][j - 1] == 1) {
                            contLive++;
                        }
                        //Direita
                        if (matrizA[i][j + 1] == 1) {
                            contLive++;
                        }
                        //Baixo
                        if (matrizA[i + 1][j] == 1) {
                            contLive++;
                        }
                        //BaixoEsquerda
                        if (matrizA[i + 1][j - 1] == 1) {
                            contLive++;
                        }
                        //BaixoDireita
                        if (matrizA[i + 1][j + 1] == 1) {
                            contLive++;
                        }
                    }
                }
                //validar o resultado e passar pra matrizB
                /*
                 - Se a celula esta VIVA e tem menos de 2 ou mais de 3 vizinhos vivos ela MORRE.
                 - Uma celula VIVA, adjacente a 2 ou 3 celulas vivas permanece VIVA.
                 - Uma celula MORTA, adjacente a 2 ou 3 celulas vivas VIVE.
                 - Se a celula esta MORTA e tem menos de 2 ou mais de 3 vizinhos vivos ela continua MORTA.
                 */
                //validacoes de viva
                synchronized (SyncB) {
                    Threads.Decrementar();
                    if (matrizA[i][j] == 1) {
                        if (contLive < 2 || contLive > 3) {
                            matrizB[i][j] = 0;
                        } else if (contLive == 2 || contLive == 3) {
                            matrizB[i][j] = 1;
                        }
                    } //validacoes de morta
                    else if (contLive == 3) {
                        matrizB[i][j] = 1;
                    } else {
                        matrizB[i][j] = 0;
                    }
                    contLive = 0;
                }
            }
        }
        matrizA = matrizB;
        matrizB = new int[m][m];
    }

    private static void Write() throws FileNotFoundException {
        PrintStream destino = new PrintStream("result.txt");

        for (int i = 0; i < matrizA.length; i++) {
            for (int j = 0; j < matrizA[0].length; j++) {
                destino.print(matrizA[i][j]);
            }
            destino.println();
        }

        for (int i = 0; i < matrizA.length; i++) {
            for (int j = 0; j < matrizA[i].length; j++) {
                System.out.print(matrizA[i][j] + " ");
            }
            System.out.println();
        }
    }

}
