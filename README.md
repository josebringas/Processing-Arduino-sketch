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

    void setup() {
      size(620,360, P2D);
      ps = new ParticleSystem();
      //so, processing will find the arduino serail port.
      myPort = new Serial(this, Serial.list()[0], 9600);
      myPort.bufferUntil('\n'); //store data into a buffer from serial.print'LN'
                               //until we see a specific character we're looking for
      spout = new Spout(this);
      spout.createSender("Spout Processing");
    }

    void draw() {
      //processing listen to arduino and store data
      background(0);
      ps.run();
      if (Integer.parseInt(trim(val)) > 0) {
        if (relleno == 0) {
            ps.addParticle();
        } else {
        }
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
