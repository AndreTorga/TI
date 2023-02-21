int buttonState;             
int lastButtonState = LOW;   
unsigned long lastDebounceTime = 0;  
unsigned long debounceDelay = 1;   
int vermelho = 5;
int azul = 4;
int verde = 3;
int buttonPin = 2;
int pin;
int num2;
int num1;
int oper;
int corRGB;
int c = 0;
void setup() {

Serial.begin(9600);
randomSeed(analogRead(0));
for (int pin = 6 ; pin<=13 ; pin++){  //ciclo para defenir todos os pins como output
      pinMode(pin, OUTPUT);   
}
pinMode(buttonPin,INPUT_PULLUP);  //butao em pullup
pinMode(vermelho, OUTPUT);  //cor red do rgb
pinMode(azul,OUTPUT);  //cor blue do rgb
pinMode(verde, OUTPUT);  //cor verde do rgb
}

void loop() {
int a = 0;
int cont = 0;
int conta;  
int b = 0;
while (c == 0){
   Serial.println("Welcome gamer!!");  //imprime apenas uma ves a cada ciclo como suposto
   Serial.println("Press the button to start");  //imprime apenas uma ves a cada ciclo como suposto
   c = 1;
}
// quando o jogador clika uma vez o valor de a muda para 1
if (a == 0){
  int reading = digitalRead(buttonPin);  //debouce para ler apenas um click como um click e nao varios clicks
  if (reading != lastButtonState){
     lastDebounceTime = millis();
  }
  if ((millis() - lastDebounceTime) < debounceDelay){
   if (reading != buttonState){
      buttonState = reading;       
        if (buttonState != HIGH){  //se o botao for desprimido(pullup) o valor de a muda para 1
           a = 1;
       }
   }   
  }
}
// como o jogador fez o click agora a = 1, entao o jogo começa
if (a == 1){
    num2 = random(1,16);  //valor random de 1 a 16(valor de representação maximo com 4 leds)
    num1 = random(1,16);  //valor random 1 a 16(valor de representação maximo com 4 leds)
    // representação do numero nos leds com operaçoes de deslocamento e interseção com 0001(1 em binario para ler o numero na primeira posição)
    for(int digit = 0; digit < 4; digit++){
  	   digitalWrite(6 + digit, (num1 >> digit)&1);  //numero 1  
       digitalWrite(10 + digit, (num2 >> digit)&1);  //numero 2
    }
   oper = random(1,4);  //valor random para corresponder a uma das opercações(xor,or,and)
    if (oper == 1){
     corRGB = 5;
     Serial.println("O operador binario e (and)");
     digitalWrite(vermelho,1);
     conta = (num2 & num1);
    }
    else if (oper == 2){
     corRGB = 4;
     Serial.println("O operador binario e (or)");
     digitalWrite(azul,1);
     conta = (num2 | num1);
    }
    else {
     corRGB = 3;
     Serial.println("O operador binario e (xor)");
     digitalWrite(verde,1);
     conta = (num2 ^ num1);
    }
 //Serial.println(conta);  //apenas para auxiliar na verificaçao do codigo(este jogo é modo hard)
 oper = random(1,4);  //redefenir um valor da operação para o proximo ciclo/jogo
 Serial.println("Tens 7 segundos para responder");
 int tempoINICIO=millis();  //guarda o valor do tempo quando o codigo "chega aqui"
 int tempoAGORA=millis();
 while((tempoAGORA-tempoINICIO)<7000){  //7 segundos para responder
    tempoAGORA=millis();  //tempo que é redefenido a cada ciclo para verificar a condição acima, se nao se verificar passa para a proxima parte
       if ((tempoAGORA-tempoINICIO)==3500){  // 50% do tempo decorrido
         Serial.println("Passou 50% do tempo");
       }
       if ((tempoAGORA-tempoINICIO)==5250){  // 75% do tempo decorrido
         Serial.println("Passou 75% do tempo");
       }  
    int reading = digitalRead(buttonPin);  //debouncezinho basico
    if (reading != lastButtonState){
     lastDebounceTime = millis();
    }
    if ((millis()-lastDebounceTime) < debounceDelay){
      if (reading!= buttonState){
        buttonState=reading;
          if (buttonState != HIGH){  //contador se o botao for despresionado(pullup)
              cont = cont + 1;
              Serial.print("Numero de cliks :");Serial.println(cont);     
          }
      }
    }
    if ((millis()-lastDebounceTime)>1000){  //reset se o botao for primido mais de 1 segundo
        if (buttonState != HIGH){
             Serial.println("Modo reset");
             for(int digit = 0; digit < 4; digit++){  //liga apenas os leds correspondentes aos numeros 1 e 2 
  	            digitalWrite(corRGB,LOW);  //para piscar o rgb
                digitalWrite(6 + digit, (num1 >> digit)&0);  //le os leds de 6 a 9
                digitalWrite(10 + digit, (num2 >> digit)&0);  //le os leds de 10 a 13
             }
             for (int i = 0; i <4; i++){  //varrimento dos leds
                for (int digit = 0; digit<4; digit++){
                  digitalWrite(corRGB,HIGH);  //liga o rgb ja acesso apenas para o ciclo acabar com o rgb desligado
                  digitalWrite(6+digit, HIGH);  //liga o led 6,7,8,9 por ordem
                  digitalWrite(13-digit, HIGH);  //liga os leds 13,12,11,10 por ordem
                  delay(500);
                  digitalWrite(corRGB, LOW);
                  delay(500);
                }    
                for (int digit = 0; digit<4; digit++){   //depois do ciclo acima, esta parte faz o "inverso
                  digitalWrite(corRGB,HIGH);
                  digitalWrite(9-digit, LOW);
                  digitalWrite(10+digit, LOW);
                  delay(500);
                  digitalWrite(corRGB, LOW);
                  delay(500);
                }
             }
             Serial.print("O jogo vai resetar em : ");//animação de reset
             delay(1000);
             Serial.print("3");
             delay(1000);
             Serial.print(" 2");
             delay(1000);
             Serial.println(" 1");
             delay(1000);
             Serial.println("Welcome gamer!!");  
             Serial.println("Press the button to start"); 
             a = 3;  
             num2 = random(1,17);
             num1 = random(1,17);
             
        }
    }
 }
if (conta == cont && a!=3){  //quando o codigo passa pelo reset o a = 3 fazendo a!=3 assim o codigo não passa por aqui//verificação se o jogador acertou
      Serial.println("Muito bem, vais passar a TI hahahahah jk");
      int b = 1;  //para nao saltar para omodo derrrota
      vitoria();  //chama a função vitoria 
}
else if (a!=3 && b!=1){  //se nao acertou entao perdeu..6//a!=3 para quando der reset nao ler esta parte do codigo
      Serial.println("E so mais uma cadeira, mais um ano...PERDEU GG");
      Serial.print("A resposta correta era :");Serial.println(conta);
      derrota();  //chama a funcao derrota
}
}
num2 = random(1,17);  //para dar novos valores random ao numero 2 no fim de cada jogo 
num1 = random(1,17);  //para dar novos valores random ao numero 1 no fim de cada jogo
c = 1;
}

void vitoria(){  //funcao vitoria
   int tempoINICIO3=millis();  //guarda o valor do tempo quando o codigo "chega aqui"
   int tempoAGORA3=millis();
   while((tempoAGORA3-tempoINICIO3)<5000){  //ciclo durante 5 segundos
     tempoAGORA3=millis();  //tempo que é redefenido a cada ciclo para verificar a condição acima
     for (int i = 0; i<5;i++){  //pisca os leds 4 vezes
      for(int digit = 0; digit < 4; digit++){  //liga apenas os leds correspondentes aos numeros 1 e 2 
  	     digitalWrite(corRGB,HIGH);  //para piscar o rgb
         digitalWrite(6 + digit, (num1 >> digit)&1);  //desliga os leds de 6 a 9
         digitalWrite(10 + digit, (num2 >> digit)&1);  //desliga os leds de 10 a 13
      }
      delay(500);  //pequeno delay
      for(int digit = 0; digit < 4; digit++){  //liga apenas os leds correspondentes aos numeros 1 e 2 
  	     digitalWrite(corRGB,LOW);  //para piscar o rgb
         digitalWrite(6 + digit, (num1 >> digit)&0);  //le os leds de 6 a 9
         digitalWrite(10 + digit, (num2 >> digit)&0);  //le os leds de 10 a 13
      }
     delay(500);
    }
   }
   delay(1000);
  for (int i = 0; i <4; i++){  //varrimento 3 vezes 
      for (int digit = 0; digit<4; digit++){
        digitalWrite(6+digit, HIGH);  //liga o led 6,7,8,9 por ordem
        digitalWrite(13-digit, HIGH);  //liga os leds 13,12,11,10 por ordem
        delay(500);
      }    
      for (int digit = 0; digit<4; digit++){  //depois do ciclo acima, esta parte faz o "inverso"
        digitalWrite(9-digit, LOW);
        digitalWrite(10+digit, LOW);
        delay(500);
      }
  }
Serial.print("O jogo vai resetar em : ");  //animação do rest 3, 2, 1
Serial.print("3");
delay(1000);
Serial.print(" 2");
delay(1000);
Serial.println(" 1");
delay(1000);
Serial.println("Welcome gamer!!");
Serial.println("Press the button to start"); 
}      

void derrota(){  //modo derrota
 for(int digit = 0; digit < 4; digit++){  //liga apenas os leds correspondentes aos numeros 1 e 2 
         digitalWrite(6 + digit, (num1 >> digit)&0);  //desliga os leds de 6 a 9
         digitalWrite(10 + digit, (num2 >> digit)&0);  //desliga 10 a 13
 }
 int tempoINICIO4=millis();  //guarda o valor do tempo quando o codigo "chega aqui"
 int tempoAGORA4=millis();
 while((tempoAGORA4-tempoINICIO4)<5000){  //faz o ciclo durante 5 segundos 
      tempoAGORA4=millis();  //tempo que é redefenido a cada ciclo para verificar a condição acima
      digitalWrite(corRGB,HIGH);  //acende o led ja acesso
      delay(500);
      digitalWrite(corRGB,LOW);  //desliga o led(eu pus o HIGH primero para o ciclo acabar com o led desligado)
      delay(500);
 }
 Serial.print("O jogo vai resetar em : ");  //animaçãozinha do reset
 delay(1000);
 Serial.print("3");
 delay(1000);
 Serial.print(" 2");
 delay(1000);
 Serial.println(" 1");
 delay(1000);
 Serial.println("Welcome gamer!!");
 Serial.println("Press the button to start"); 
}


