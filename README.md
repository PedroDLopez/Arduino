# Arduino
//Arduino sketchs
// Semáforos: Version 8 
// Utilizando funciones con parámetros: semaforo() y on_off_led().
// Agregado de boton para cruce de ciegos y buzzer temporizados.
// Autor: Ing. Pedro Lopez
// Date:  ABR 2019

#define V1 13  
#define A1 12
#define R1 11
#define V2 10  
#define A2 9
#define R2 8
#define BOTON 2
#define BUZZER 0

#define T1 4000   // tiempo de luz Verde.
#define T2 1000   // tiempo de lus Amarilla.

volatile int estado = LOW;  // variable que detecta cuando cambia de estado del boton.

void setup() {
    // En lugar de tener muchas funciones pinMode, hacemos los siguiente:
    for(int i=8; i<=13; i++){
      pinMode(i, OUTPUT);
    }
    pinMode(BOTON, INPUT);
    attachInterrupt(digitalPinToInterrupt(BOTON), manejadorPulsador, RISING);   
    pinMode(BUZZER, OUTPUT);
}

void loop() {
  if( estado == LOW)
      semaforos();
  else
      crucePeaton();
}

void semaforos(){
  semaforo(R2, R1, A1, V1);   // Trabaja semaforo 1, semaforo 2 en Rojo.
  semaforo(R1, R2, A2, V2);   // Trabaja semaforo 2, semaforo 1 en Rojo.
}

void semaforo(int RR, int R, int A, int V){
                            // Primera llamada a semaforo       // segunda llamada a semaforo
  digitalWrite(RR, HIGH);   // R2 encendido                     // R1 encendido
  digitalWrite(R, HIGH);    // R1 encendido                     // R2 encendido
  delay(T1/10);
  on_off_led(A, T2);        // A1 enciende y apaga              // A2 enciende y apaga
  digitalWrite(R, LOW);     // R1 apagado                       // R2 apagado
  on_off_led(V, T1);        // V1 enciende y apaga              // V2 enciende y apaga
  on_off_led(A, T2);        // A1 enciende y apaga              // A2 enciende y apaga
}

void on_off_led(int led, int tempo1){
  digitalWrite(led, HIGH);
  delay(tempo1); 
  digitalWrite(led, LOW);
}

void manejadorPulsador(){
      estado = !estado;
}

void crucePeaton(){
      // poner en rojo los dos semaforos por 5 segundos
      // al tiempo hacer sonar repatidamente el buzzer
      apagarTodo();
      digitalWrite(R1, HIGH);
      digitalWrite(R2, HIGH);
      int tiempo = 1;
      while(tiempo < 10){
          digitalWrite(BUZZER, HIGH);
          delay(300);
          digitalWrite(BUZZER, LOW);
          delay(100);
          tiempo = tiempo + 1;
      }
      estado = LOW;
}

void apagarTodo(){           // apaga todos los leds de los semaforos
    for(int i=8; i<=13; i++)
        digitalWrite(i, LOW);
}
