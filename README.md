# Checkpoint-2-Edge-Computing-and-Computer-Systems
**Nome dos integrantes do grupo:** </br>
*Julia Azevedo Lins RM98690* </br>
*Luis Gustavo Barreto Garrido RM99210* </br>
*Luan Silveira Macéa RM98290* </br>
*Felipe Cortez dos Santos RM99750* </br>
*Victor Hugo Aranda Forte RM99667* </br>

**Turma: (ESPW)**

**Ano: 2023**

## Objetivo
Vocês foram contratados pela Vinheria Agnello para desenvolver um sistema de monitoramento a ser instalado no ambiente em que os vinhos são armazenados. O dono a Vinheria informou que a qualidade do vinho é influenciada diretamente pelas condições de temperatura, umidade e luminosidade do ambiente.
Dado que já auxiliamos anteriormente com a questão da luminosidade agora iremos fazer o pacote completo e seguir com a montagem de um sistema arduino que capture todas essas informções.

## Descrição do desafio
- Precisam medir a temperatura e umidade do ambiente, utilizando um sensor integrado DHT11, precisamos aprender a instalar e utilizar esse sensor no IDE do Arduino para ler a temperatura e umidade do ambiente.
- Precisamos aprender a utilizar um display de LCD para exibir esses valores e integrar ele com o sistema já criado de funcionalidade para temperatura e umidade além de sinalizar com os LEDs e o Buzzer a luminosidade indicar quando a temperatura e/ou a umidade estiverem em níveis críticos.

## Desenvolvimento do projeto
   - Enfrentamos alguns desafios durante o desenvolvimento da prioridade de acontecimentos para o arduino retornar de forma responsiva com os leds e o buzzer de acordo com o cenario apresentado.

 ### Esquema de montagem ###
 ![Checkpoint II Case Vinheria Grupo 3](https://user-images.githubusercontent.com/84590776/234154228-9e834062-8747-4f8e-8ece-63cafe5e7fc5.png)
  ### Codigo Utilizado ###
  ```
  #include <LiquidCrystal.h>

int seconds = 0;
const int analogIn = A0;
int humiditysensorOutput = 0;
int RawValue= 0;
double Voltage = 0;
double tempC = 0;
double tempF = 0;
LiquidCrystal lcd_1(12, 11, 5, 4, 3, 2);

const int buzzerPin = 7; // Alarme no pino 7
const int ldrPin = 2; // LDR no pino analógico 2
const int ledVerde = 8;
const int ledAmarelo = 9;
const int ledVermelho = 10;
int ldrValue = 0; // Valor lido do LDR
const int freq = 5; // altera frequencia do sonorizador

void setup()
{
  Serial.begin(9600);
  pinMode(A1, INPUT);
  pinMode(buzzerPin, OUTPUT);
  pinMode(ledVerde,OUTPUT);
  pinMode(ledAmarelo,OUTPUT);
  pinMode(ledVermelho,OUTPUT);
}


void loop()
{
  ldrValue = analogRead(ldrPin);
  Serial.println(ldrValue);
  delay(50);

  RawValue = analogRead(analogIn);
  Voltage = (RawValue / 1023.0) * 5000; 
  tempC = (Voltage-500) * 0.1; 
  tempF = (tempC * 1.8) + 32; 
  Serial.print("Raw Value = " );                  
  Serial.print(RawValue);      
  Serial.print("\t milli volts = ");
  Serial.print(Voltage,0); //
  Serial.print("\t Temperature in C = ");
  Serial.print(tempC,1);
  Serial.print("\t Temperature in F = ");
  Serial.println(tempF,1);
  humiditysensorOutput = analogRead(A1);
  Serial.print("Humidity: "); 
  Serial.print(map(humiditysensorOutput, 0, 1023, 10, 70));
  Serial.println("%");

  //Luminosidade Alta (Esse valor poderá ser alterado dependendo do LDR utilizado e do local)
  if (ldrValue > 950){
    apagaLeds();
    digitalWrite(ledVermelho,HIGH); //Toca o alarme
    tone(buzzerPin,1000); // toca um tom de 1000 Hz
    lcd_1.begin(16, 2);
    lcd_1.print("Luminosidade");
    lcd_1.setCursor(0, 1);
    lcd_1.print("Muito clara");
    delay(5000);
  }
  //Luminosidade média.
  if (ldrValue >= 200 && ldrValue <= 950) {
    apagaLeds();
    digitalWrite(ledAmarelo,HIGH);
    lcd_1.begin(16, 2);
    lcd_1.print("Luminosidade");
    lcd_1.setCursor(0, 1);
    lcd_1.print("A meia luz");
    delay(5000);
  }
  //Luminosidade Baixa.
  if (ldrValue < 200) {
    apagaLeds();
    digitalWrite(ledVerde,HIGH);
    lcd_1.begin(16, 2);
    lcd_1.print("Luminosidade");
    lcd_1.setCursor(0, 1);
    lcd_1.print("Baixa");
    delay(5000);
  }

  //temperatura ok.
  if (10 < tempC && tempC < 15) {
    apagaLeds();
    digitalWrite(ledVerde,HIGH);
    lcd_1.begin(16, 2);
    lcd_1.print("Temperatura OK");
    lcd_1.setCursor(0, 1);
    lcd_1.print(tempC,1);
    delay(5000);
  }

  //temperatura Alta.
  if (tempC > 15) {
    apagaLeds();
    digitalWrite(ledAmarelo,HIGH);
    tone(buzzerPin,1000); // toca um tom de 1000 Hz
    lcd_1.begin(16, 2);
    lcd_1.print("Temperatura Alta");
    lcd_1.setCursor(0, 1);
    lcd_1.print(tempC,1);
    delay(5000);
  }

  //temperatura Baixa.
  if (tempC < 10) {
    apagaLeds();
    digitalWrite(ledAmarelo,HIGH);
    tone(buzzerPin,1000); // toca um tom de 1000 Hz
    lcd_1.begin(16, 2);
    lcd_1.print("Temperatura Baixa");
    lcd_1.setCursor(0, 1);
    lcd_1.print(tempC,1);
    delay(5000);
  }

//Humidade ok.
  if (50 < (map(humiditysensorOutput, 0, 1023, 10, 70))&& (map(humiditysensorOutput, 0, 1023, 10, 70)) < 70) {
    apagaLeds();
    digitalWrite(ledVerde,HIGH);
    lcd_1.begin(16, 2);
    lcd_1.print("Humidade OK");
    lcd_1.setCursor(0, 1);
    lcd_1.print(map(humiditysensorOutput, 0, 1023, 10, 70));
    lcd_1.print("%");
    delay(5000);
  }

  //Humidade Baixa.
  if (map(humiditysensorOutput, 0, 1023, 10, 70) < 50) {
    apagaLeds();
    digitalWrite(ledVermelho,HIGH);
    tone(buzzerPin,1000); // toca um tom de 1000 Hz
    lcd_1.begin(16, 2);
    lcd_1.print("Humidade Baixa");
    lcd_1.setCursor(0, 1);
    lcd_1.print(map(humiditysensorOutput, 0, 1023, 10, 70));
    lcd_1.print("%");
    delay(5000);
  }

  //Humidade Alta.
  if (map(humiditysensorOutput, 0, 1023, 10, 70) >= 70) {
    apagaLeds();
    digitalWrite(ledVermelho,HIGH);
    tone(buzzerPin,1000); // toca um tom de 1000 Hz
    lcd_1.begin(16, 2);
    lcd_1.print("Humidade Alta");
    lcd_1.setCursor(0, 1);
    lcd_1.print(map(humiditysensorOutput, 0, 1023, 10, 70));
    lcd_1.print("%");
    delay(5000);
  }
  
  if (!(ldrValue > 950) && (10 < tempC && tempC < 15) && (50 < (map(humiditysensorOutput, 0, 1023, 10, 70))&& (map(humiditysensorOutput, 0, 1023, 10, 70)) < 70)){
   noTone(buzzerPin); 
  }


}
//Função criada para apagar todos os leds de uma vez.
void apagaLeds() {
  digitalWrite(ledVerde,LOW);
  digitalWrite(ledAmarelo,LOW);
  digitalWrite(ledVermelho,LOW);
}
```
  

  ## Pré-requisitos
  
![image](https://user-images.githubusercontent.com/84590776/234154078-cbe0ae71-71ef-415d-9127-98cf6cdd8fae.png)

