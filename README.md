# Processing-Arduino-sketch
Basic interaction between Processing and Arduino using piezo sensor // Interacción básica entre arduino y Processing usando un sensor piezo

Parte de Processing / Processing part

    import processing.serial.*;
    import spout.*;

    Spout spout;
    ParticleSystem ps;
    Serial myPort; //create class from serial class. listen to any serial communication
    String val ="0"; //data received from the serial port; from arduino.
    boolean firstContact = false;
    int relleno =0;
    int R = 150;
    int G = 100;
    PImage frog;

    void setup() {
      size(620,360, P2D);
      ps = new ParticleSystem();
      frog = loadImage("frog.jpg");
      //so, processing will find the arduino serail port.
      myPort = new Serial(this, Serial.list()[0], 9600);
      myPort.bufferUntil('\n'); //store data into a buffer from serial.print'LN'
                               //until we see a specific character we're looking for
      spout = new Spout(this);
      spout.createSender("Spout Processing");
      
      background(0);
    }

    void draw() {
      //processing listen to arduino and store data
      ps.run();
      if (Integer.parseInt(trim(val)) > 30) {
        ps.addParticle2();
        println("--------------------------------------"); //para detectar entrada
    } else if (Integer.parseInt(trim(val)) > 0) {
        ps.addParticle();
        println("waaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa");
      }
      spout.sendTexture();
    }

    void serialEvent (Serial myPort) { //gets called everytime there's a specific data
      val = myPort.readStringUntil('\n');
      if (val !=null) { //make sure our data inst empty
        val = trim(val); //erase/remove whitespace
        println(val); 

        if (firstContact == false) {
          if (val.equals("A")) {
            myPort.clear();
            firstContact = true;
            myPort.write("A");
            println("contact");
        }
      }
      else {
        println(val);
        if (mousePressed == true) {
          myPort.write('1');
          println("1");
        }
        myPort.write("A");
      }
     }
    }
    
Clase / Class

        class Ball {
          PVector location;
          PVector velocity;
          PVector acceleration;
          int diameter;

          float lifespan = 155;

          Ball(PVector inicial) {
            //location = new PVector(width/2, 0);
            location = inicial.get();
            velocity = new PVector(random(-2,2),random(-2,2));
            acceleration = new PVector(0.001,0.05);
            diameter = 6;
          }

          boolean isDead() {
            if (lifespan <= 0) {
              return true;
            } else {
              return false;
            }
          }

          void display() {
           color c = frog.get(int(location.x),int(location.y));
           stroke(c,lifespan);
           fill(c,lifespan);
           ellipse(location.x, location.y, diameter, diameter); 
          }

          void update() {
           location.add(velocity); 
           velocity.add(acceleration);
           lifespan -= 1;
          }
        }

Lista de arreglo // Arraylist

    class ParticleSystem {
     ArrayList<Ball> b;

     ParticleSystem() {
      b = new ArrayList<Ball>();

     }

      void run() {

      for (int i =  b.size()-1; i >= 0; i--) {
        Ball fall =  b.get(i);
        fall.display();
        fall.update();
        if (fall.isDead()) {
        b.remove(i); 
        }
       }
      }

      void addParticle() {
        for (int i = 0; i < 10; i++) {
        b.add(new Ball(new PVector(mouseX, mouseY)));
      }
     }

     void addParticle2() {
        for (int i = 0; i < 80; i++) {
        b.add(new Ball(new PVector(mouseX, mouseY)));
      }
     }
    }
    
Parte Arduino

    int DRUM1 = 0;
    int val = 0;

    void setup() {
      Serial.begin(9600);
    }

    void loop() {
      val = analogRead(DRUM1);  
      Serial.println(val, DEC);
      delay(10);
    }
