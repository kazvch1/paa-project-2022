package testmaze;

import java.awt.*;
import java.awt.event.*;
import java.util.*;
import javax.swing.*;

//Riswan Arta Kurnia 2001020018
//Kasirajil Masykur 2001020009
//
public class Maze extends JFrame {
    //obstacle
    final static int X = 1;
    //free space
    final static int C = 0;
    //initial state
    final static int S = 2;
    //goal
    final static int E = 8;
    // the path
    final static int V = 9;
    int c1;
    int cxs = 1;
    int c2;
    int cys = 1;
    int c3;
    int cxd = 8;
    int c4;
    int cyd = 8;
    //initial state   (i,j)
    static int START_I = 1, START_J = 1;
    //goal  (i,j)
    static int END_I = 8, END_J = 8;
    
    int[][] maze = new int[][]{ // the initial array for the maze
        {1, 1, 1, 1, 1, 1, 1, 1, 1, 1},
        {1, 2, 0, 0, 0, 0, 0, 0, 0, 1},
        {1, 0, 0, 0, 0, 0, 0, 0, 0, 1},
        {1, 0, 0, 0, 0, 0, 0, 0, 0, 1},
        {1, 0, 0, 0, 0, 0, 0, 0, 0, 1},
        {1, 0, 0, 0, 0, 0, 0, 0, 0, 1},
        {1, 0, 0, 0, 0, 0, 0, 0, 0, 1},
        {1, 0, 0, 0, 0, 0, 0, 0, 0, 1},
        {1, 0, 0, 0, 0, 0, 0, 0, 8, 1},
        {1, 1, 1, 1, 1, 1, 1, 1, 1, 1}
    };

    // for random array which we will store the randomly generated array in.
    int[][] arr;

    // Buttons For GUI (still not initialized)
    JButton solveStack;
    JButton clear;
    JButton genRandom;
    boolean repaint = false;  //(line:349 in paint) the value will define what we want to paint on the Jframe, solve the maze or paint the maze

   // take copy of the original maze, used when we want to remove (clear) the solution from the JFrame   
   int[][] savedMaze = clone();

    // the maze class constructor, this will be the first thing to be executed when we creat an object from this class
    public Maze() {

        setTitle("Maze");     //Title For JFrame
        setSize(960, 530);    // Size For JFrame  (width,height)
        
        setLocationRelativeTo(null);                                                // to make JFrame appear in the Middle of the screen 
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);                             // To close the app when click on exit or (X)
        setLayout(null);                                                            // to set the position of the components (like JLabel and JTextField, Buttons) and by hand (we will choose the position by ourselves)

        // initialize objects for Jlabel and JTextField

        // initialize objects for Buttons
        solveStack = new JButton("Solve DFS");
        clear = new JButton("Clear");
        genRandom = new JButton("Generate Random Maze");

        // Add The Buttons to JFrame
        add(solveStack);
        add(clear);
        add(genRandom);

        // make JFrame visible (it's invisible by default, we don't know why!!)
        setVisible(true);

        // set the positions of the components on the JFrame (x,y,width,height). here we chose the position by hand, this is why we sat the set Layout to null
        solveStack.setBounds(500, 50, 100, 40);
        clear.setBounds(650, 50, 100, 40);
        genRandom.setBounds(500, 150, 170, 40);

        // what happen when click on Generate Random Maze Button
        genRandom.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {

                int x[][] = GenerateArray();
                repaint = true;
                random_value();
                restore(x,cxs,cys,cxd,cyd);
                repaint();   // repaint the maze on the JFrame
            }
        });

        // what happen when click on Clear Button
        clear.addActionListener(new ActionListener() {

            @Override
            public void actionPerformed(ActionEvent e) {

                if (arr == null) {       
                    repaint = true;      
                    restore(savedMaze,cxs,cys,cxd,cyd);
                    repaint();           // repaint the maze (savedMaze) on the JFrame
                } else {                 
                    repaint = true;      
                    restore(arr,cxs,cys,cxd,cyd); 
                    repaint();           // repaint the maze on the JFrame
                }
            }
        });

        // what happen when click on DFS Button
        solveStack.addActionListener(new ActionListener() {

            @Override
            public void actionPerformed(ActionEvent e) {
                if (arr == null) {     
                    restore(savedMaze,cxs,cys,cxd,cyd);  
                    repaint = false;     
                    solveStack();       
                    repaint();   // repaint the maze on the JFrame
                    
                } else {                 // happens if a random array was generated
                    
                    restore(arr,cxs,cys,cxd,cyd);        
                    repaint = false;     
                    solveStack();        
                   repaint();    // repaint the maze on the JFrame
                    

                }

            }
        });


    }

    // get size of the maze
    public int Size() {
        return maze.length;
    }
    
    //create random value for source and destination
    public void random_value(){
        c1 = (int) (Math.random() * 1000);
        cxs = c1 / 100;
        c2 = (int) (Math.random() * 1000);
        cys = c2 / 100;
        c3 = (int) (Math.random() * 1000);
        cxd = c3 / 100;
        c4 = (int) (Math.random() * 1000);
        cyd = c4 / 100;
    }
    
    public void Print() {
        for (int i = 0; i < Size(); i++) {      
            for (int J = 0; J < Size(); J++) {  
                System.out.print(maze[i][J]);   
                System.out.print(' ');         
            }
            System.out.println();               
        }
        
    }

    //return true if cell is within maze 
    public boolean isInMaze(int i, int j) {  

        if (i >= 0 && i < Size() && j >= 0 && j < Size()) {
            return true;
        } else {
            return false;
        }
    }

    public boolean isInMaze(MazePos pos) {   
        return isInMaze(pos.i(), pos.j());   
    }

    
    public int mark(int i, int j, int value) {
        assert (isInMaze(i, j));  
        int temp = maze[i][j];    
        maze[i][j] = value;       
        return temp;              
    }

    public int mark(MazePos pos, int value) {   
        return mark(pos.i(), pos.j(), value);   
    }

    // return true if the node equal to v=9 (Green, Explored)
    public boolean isMarked(int i, int j) {
        assert (isInMaze(i, j));
        return (maze[i][j] == V);

    }

    public boolean isMarked(MazePos pos) {   
        return isMarked(pos.i(), pos.j());
    }

    // return true if the node is equal to 0 (White, Unexplored)
    public boolean isClear(int i, int j) {
        assert (isInMaze(i, j));
        return (maze[i][j] != X && maze[i][j] != V);

    }

    public boolean isClear(MazePos pos) {   
        return isClear(pos.i(), pos.j());   
    }

    // to make sure if it is reach the goal (Goal Test)
    public boolean isFinal(int i, int j) {
        Maze.END_I = cxd;
        Maze.END_J = cyd;
        return (i == Maze.END_I && j == Maze.END_J);
    }

    public boolean isFinal(MazePos pos) {  
        return isFinal(pos.i(), pos.j());  
    }

    // make Copy from the original maze
    public int[][] clone() {
        int[][] mazeCopy = new int[Size()][Size()];
        for (int i = 0; i < Size(); i++) {
            for (int j = 0; j < Size(); j++) {
                mazeCopy[i][j] = maze[i][j];
            }
        }
        return mazeCopy;
    }


    // to restore the maze to the initial state 
    public void restore(int[][] savedMazed, int cxs, int cys, int cxd, int cyd) {
        for (int i = 0; i < Size(); i++) {
            for (int j = 0; j < Size(); j++) {
                maze[i][j] = savedMazed[i][j];
            }
        }
        
            maze[cxs][cys] = 2;
            maze[cxd][cyd] = 8;
    }

    //generate random maze whith values 0 and 1 (black and white blocks)
    public int[][] GenerateArray() {
        arr = new int[10][10];
        Random rnd = new Random();

        int high = 1;

        for (int i = 0; i < 10; i++) {
            for (int j = 0; j < 10; j++) {
                int n = rnd.nextInt(high + 1) ;
                arr[i][j] = n;
            }
        }
          //left initial
          if(cxs-1 != -1){
              arr[cxs-1][cys] = 0;
          }
          //top initial
          if(cys-1 != -1){
              arr[cxs][cys-1] = 0;
          }
          //right initial
          if(cxs+1 != 10){
            arr[cxs+1][cys] = 0;
          }
          //bottom initial
          if(cys+1 != 10){
            arr[cxs][cys+1] = 0;
          }
          
          //make sure all paths from initial state are legal moves (white block)
          
          //left dest
          if(cxd-1 != -1){
          arr[cxd-1][cyd] = 0;
          }
          //top dest
          if(cyd-1 != -1){
          arr[cxd][cyd-1] = 0;
          }
          //right dest
          if(cxd+1 != 10){
          arr[cxd+1][cyd] = 0;
          }
          if(cyd+1 != 10){
          arr[cxd][cyd+1] = 0;
          }
        return arr;
    }
    
    //draw the maze on the JFrame
    @Override
    public void paint(Graphics g) {
        super.paint(g);
        g.translate(70, 70);      //move the maze to begin at 70 from x and 70 from y
        
        // draw the maze
        if (repaint == true) {  // what to do if the repaint was set to true (draw the maze as a problem without the solution)
            for (int row = 0; row < maze.length; row++) {
                for (int col = 0; col < maze[0].length; col++) {
                    Color color;
                    switch (maze[row][col]) {
                        case 1:
                            color = Color.darkGray;       // block (black)
                            break;
                        case 8:
                            color = Color.RED;          // goal (red)
                            break;
                        case 2:
                            color = Color.YELLOW;      //initial state   (yellow)
                            break;
                        //   case '.' : color=Color.ORANGE; break;
                        default:
                            color = Color.WHITE;        // white free space 0  (white)
                    }
                    g.setColor(color);
                    g.fillRect(40 * col, 40 * row, 40, 40);    // fill rectangular with color 
                    g.setColor(Color.BLUE);                    //the border rectangle color
                    g.drawRect(40 * col, 40 * row, 40, 40);    //draw rectangular with color

                }
            }
        }
        
        if (repaint == false) {   // what to do if the repaint was set to false (draw the solution for the maze)
            for (int row = 0; row < maze.length; row++) {
                for (int col = 0; col < maze[0].length; col++) {
                    
                    Color color;
                    switch (maze[row][col]) {
                        case 1:
                            color = Color.darkGray;     // block (black)          
                            break;
                        case 8:
                            color = Color.RED;         // goal  (red)
                            break;
                        case 2:
                            color = Color.YELLOW;      //initial state   (yellow)
                            break;
                        case 9:
                            color = Color.green;   // the path from the initial state to the goal
                            break;
                        default:
                            color = Color.WHITE;   // white free space 0  (white)
                    }
                    g.setColor(color);
                    g.fillRect(40 * col, 40 * row, 40, 40);  // fill rectangular with color 
                    g.setColor(Color.BLUE);                  //the border rectangle color
                    g.drawRect(40 * col, 40 * row, 40, 40);  //draw rectangular with color
                }
            }
        }
    }
    

    public static void main(String[] args) {  // the main program

        SwingUtilities.invokeLater(new Runnable() { 
            @Override                                
            public void run() {
                Maze maze = new Maze();
            }
        });

    }

    public void solveStack() { //DFS correspond to Stack
        // start of the time
        //create stack of MazPos (MazPos (the node) is what we will be pushing and popping from the stack)
        Stack<MazePos> stack = new Stack<MazePos>();
        
        //insert the start node
        //source
        Maze.START_I = cxs;
        Maze.START_J = cys;
        //destination
        Maze.END_I = cxd;
        Maze.END_J = cyd;
        stack.push(new MazePos(START_I, START_J));
        MazePos crt;   //current node
        MazePos next;  //next node
        while (!stack.empty()) {//while stack not empty
            
            //get current position by popping from stack
            crt = stack.pop();
            if (isFinal(crt)) { //if the goal is reached then exit, no need for further exploration.
                break;
            }
            
             //priority : down > left > right > up
            //mark the current position as explored
            mark(crt, V);

            //push its neighbours in the stack
            next = crt.north();    // go up from the current node
            if (isInMaze(next) && isClear(next)) {
                stack.push(next);
            }
            next = crt.east();    //go right from the current node
            if (isInMaze(next) && isClear(next)) {
                stack.push(next);
            }
            next = crt.west();    //go left from the current node
            if (isInMaze(next) && isClear(next)) {
                stack.push(next);
            }
            next = crt.south();  // go down from the current node
            if (isInMaze(next) && isClear(next)) {
                stack.push(next);
            }
        }


        System.out.println("\nDFS Method: ");
        Print();

    }

    

        
    

}
