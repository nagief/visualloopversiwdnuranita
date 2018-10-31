/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */
package visualloop1;

// Menambahkan package baru
import java.util.ArrayList;
import javafx.application.Application;
import javafx.event.EventHandler;
import javafx.scene.Group;
import javafx.scene.Scene;
import javafx.scene.canvas.Canvas;
import javafx.scene.canvas.GraphicsContext;
import javafx.scene.image.Image;
import javafx.scene.input.KeyEvent;
import javafx.scene.input.MouseEvent;
import javafx.scene.paint.Color;
import javafx.stage.Stage;
/**
 *
 * @author nagief
 */
public class VisualLoop1 extends Application implements Runnable {
    
    //Membuat LOOP PARAMETERS
    private final static int MAX_FPS = 60;
    private final static int MAX_FRAME_SKIPS = 5;
    private final static int FRAME_PERIOD = 1000/MAX_FPS;
    
    //Membuat THREAD
    private Thread thread;
    private volatile boolean running = true;
    
    //Atribut Kotak
    float sisi = 100f;
    float sudutRotasi = 0f ;
    float cx = 200;
    float cy = 0;
    
    //Atribut GJB
   float g = 0.1f;
   float t = 0f;
   float v = 0f;
   float vUP = 10f;
    
    //Membuat Canvas
    Canvas canvas = new Canvas (1024,700);
    
    //Membuat KEYBOARD HANDLER
    ArrayList<String> inputKeyboard = new ArrayList<String>();
    
    public VisualLoop1() {
        resume();
    }
    
    @Override
    public void start(Stage primaryStage) {
        Group root = new Group();
        Scene scene = new Scene(root);canvas = new Canvas (1024,700);
        root.getChildren().add(canvas);
        
        //Membuat HANDLING KEYBOARD EVENT
        scene.setOnKeyPressed(
          new EventHandler<KeyEvent>(){
              public void handle (KeyEvent e){
                  String code = e.getCode().toString();
                  if (!inputKeyboard.contains(code))
                      inputKeyboard.add(code);
                  System.out.println(code);
              }
          });
        scene.setOnKeyReleased(
         new EventHandler<KeyEvent>() {
             public void handle(KeyEvent e){
                 String code = e.getCode().toString();
                 inputKeyboard.remove(code);
             }
         });
        
        //Membuat fungsi HANDLING MOUSE
        scene.setOnMouseClicked(
            new EventHandler<MouseEvent>(){
                public void handle(MouseEvent e){
                    
                }
            });
        
        //primaryStage.getIcons().add(new Image(getClass().getResourceAsStream("logo.jpg")));
        primaryStage.setTitle("Visual Loop");
        primaryStage.setScene(scene);
        primaryStage.show();
        
    }
    

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        launch(args);
    }
    
    //Membuat THREAD
    private void resume(){
        reset();
        thread = new Thread (this);
        running = true;
        thread.start();
    }
    
    //Membuat THREAD
    private void pause() {
        running = false;
        try{
        thread.join();
        }catch(InterruptedException e) {
            e.printStackTrace();
        }
    }
    
    //Membuat LOOP
    private void reset(){
        
    }
    
    //Membuat LOOP
    private void update(){
    
    //Fungsi menggerakkan Kotak ke kiri
    if (inputKeyboard.contains("LEFT")){
        cx-=2;
    }else
     //Fungsi menggerakkan Kotak ke kanan
    if (inputKeyboard.contains("K")){
        cx+=2;
    }else 
        //Fungsi menggerakkan Kotak ke atas
    if (inputKeyboard.contains("UP")){
        cy-=2;
    }else
        //Fungsi menggerakkan Kotak ke bawah
    if (inputKeyboard.contains("DOWN")){
        cy+=2;
    }
    //Fungsi menggerakkan Kotak Berotasi
    if (inputKeyboard.contains("R")){
        sudutRotasi+=2;
    }
    
    //Jatuh
    if (cy<canvas.getHeight()-0.5f*sisi){
    t++;
    v = g*t;
    cy+=v;
        
    }
         if (inputKeyboard.contains("SPACE")){
             t=0;
             cy-=vUP;
         }

    
    }
    
    //Membuat LOOP
    private void draw(){
        try{
            if(canvas != null){
                GraphicsContext gc = canvas.getGraphicsContext2D();
                gc.clearRect(0,0, canvas.getWidth(), canvas.getHeight());
                
                //Cara Menggambar Kotak
                gc.save();
                gc.translate(cx,cy);
                gc.rotate(sudutRotasi);
                gc.setFill(Color.PINK);
                gc.fillRect(-0.5f*sisi, -0.5f*sisi, sisi, sisi);
                gc.restore();
                        
                
                //CEK INPUT KEYBOARD
                
                if(inputKeyboard.contains("RIGHT")){
                    gc.setFill(Color.RED);
                    gc.fillText("RIGHT", 300, 300);
                }
                if(inputKeyboard.contains("LEFT")){
                    gc.setFill(Color.RED);
                    gc.fillText("LEFT", 300, 300);
                }
                if(inputKeyboard.contains("UP")){
                    gc.setFill(Color.RED);
                    gc.fillText("UP", 300, 300);
                }
                if(inputKeyboard.contains("DOWN")){
                    gc.setFill(Color.RED);
                    gc.fillText("DOWN", 300, 300);
                }
                if(inputKeyboard.contains("R")){
                    gc.setFill(Color.RED);
                    gc.fillText("R", 300, 300);
                }
            }
        }catch (Exception e){
            e.printStackTrace();
        }
        
    }
    
    @Override
    public void run(){
            long beginTime;
            long timeDiff;
            int sleepTime = 0;
            int framesSkipped;
            
            //LOOP WHILE running = true;
            while(running) {
                try{
                    synchronized (this){
                    beginTime = System.currentTimeMillis();
                    framesSkipped = 0;
                    update ();
                    draw ();
                }
                    timeDiff = System.currentTimeMillis()-beginTime;
                    sleepTime = (int) (FRAME_PERIOD - timeDiff);
                    if (sleepTime > 0){
                        try{
                            Thread.sleep(sleepTime);
                        }catch (InterruptedException e){
                            
                        }
                    }
                    
                    while (sleepTime  < 0 && framesSkipped < MAX_FRAME_SKIPS){
                        update();
                        sleepTime+= FRAME_PERIOD;
                        framesSkipped++;
                    }
                  
                }finally{
                    
                }
            }
    }
    
}
