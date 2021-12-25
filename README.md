# Calculator-With-Arduino (Using StackArray.h)
This project represents a calculator with Arduino Uno. It can calculate long-expression correctly like 1+2-3*2/4 ......
```
***code
#include <StackArray.h>
#include <Keypad.h>
#include <Wire.h> 
#include <LiquidCrystal.h>


LiquidCrystal lcd(A0, A1, A2, A3, A4, A5);

long first = 0;
long second = 0;
double total = 0;
char customKey;
const byte ROWS = 4;
const byte COLS = 4;
String infix = "";
String screan = "";

char keys[ROWS][COLS] = {
  {'1','2','3','+'},
  {'4','5','6','-'},
  {'7','8','9','*'},
  {'C','0','=','/'}
};
byte rowPins[ROWS] = {9,8,7,6}; //connect to the row pinouts of the keypad
byte colPins[COLS] = {5,4,3,2}; //connect to the column pinouts of the keypad


//initialize an instance of class NewKeypad
Keypad customKeypad = Keypad( makeKeymap(keys), rowPins, colPins, ROWS, COLS); 

void setup()
{  
pinMode(LED_BUILTIN, OUTPUT);

lcd.begin(16, 2);               // start lcd
for(int i=0;i<=3;i++);
lcd.setCursor(0,0);
  lcd.print("Calculator");
  lcd.setCursor(0,1);
  lcd.print("by Khaled Taha");
delay(4000);
lcd.clear();
lcd.setCursor(0, 0);
}


void loop()
{

  customKey = customKeypad.getKey();
  switch(customKey) 
  {
  case '0' ... '9': // This keeps collecting the first value until a operator is pressed "+-*/"
    lcd.setCursor(0,0);
    infix += customKey;
    screan += customKey;
    lcd.clear();
    lcd.print(screan);
    break;

  case '+':
    screan += "+";
    infix += " + ";
    lcd.clear();
    lcd.print(screan);
    break;

  case '-':
    screan += "-";
    infix += " - ";
    lcd.clear();
    lcd.print(screan);
    break;

  case '*':
    screan += "*";
    infix += " * ";
    lcd.clear();
    lcd.print(screan);
    break;

  case '/':
    screan += "/";
    infix += " / ";
    lcd.clear();
    lcd.print(screan);
    break;

  case 'C':
    total = 0;
    screan = "";
    infix = "";
    lcd.clear();
    break;

    case '=':
     lcd.setCursor(0,1);
     double result = evaluate(infix);
     lcd.print(result);
     break;
  }
}

double evaluate(String exString){
        exString = infix_To_Prefix(exString);
        StackArray <double> stc;
        static double result = 0;
        static double number = 0;
        int numDecimal = -1;
        int i = 0;
        
        while(i < exString.length()){
            
            if((exString.charAt(i) >= 48 && exString.charAt(i) <= 57)){
                
                while((exString.charAt(i) >= 48 && exString.charAt(i) <= 57) || exString.charAt(i) == '.'){
                    
                    if(exString.charAt(i) == '.') numDecimal = 0;
                    else {
                    number = (number * 10) + (double)(exString.charAt(i) - '0');
                    if(numDecimal > -1) numDecimal++;
                    }
                    i++;
                }
              
                if(numDecimal == -1) numDecimal = 0;
                int ten = 1;
                for(int i = 1; i <= numDecimal; i++) ten *= 10;
                number /= ten;
                
                stc.push(number);
                numDecimal = -1;
                number = 0;
            }
            
            
            else if (exString.charAt(i) != 32){

                double operand1 = stc.peek();

                stc.pop();
                double operand2 = stc.peek();

                stc.pop();
                
                result = mathOperator(operand2, operand1, exString.charAt(i));

                stc.push(result);
            }
            i++;
        }
        
        return stc.peek();
    }

    
String infix_To_Prefix(String expression){
        StackArray <char> stc;
  String output = "";
         int i = 0;
        
        while(i < expression.length()){
            
            if((expression.charAt(i) >= 48 && expression.charAt(i) <= 57) || expression.charAt(i) == 32
                    || expression.charAt(i) == '.'){
                      output += expression.charAt(i);
            }
            
            else if(expression.charAt(i) == '(') stc.push('(');
            else if(expression.charAt(i) == ')'){
                while(stc.peek() != '('){
            output += " ";
            output += stc.peek();
                    stc.pop();
                }
                stc.pop();
            }
            
            else{
              
            while(!stc.isEmpty()  && precedent_process(expression.charAt(i)) <= precedent_process(stc.peek())){
            output += " ";
            output += stc.peek();
                stc.pop();
                
            }
            
            stc.push(expression.charAt(i));
            }
            i++;
        }
        Serial.println(output);
        while(!stc.isEmpty()) {
            output += " ";
            output += stc.peek();
            Serial.println(output);
            stc.pop();
        }
        return output;
    }

    
 int precedent_process(char c){
        if(c == '+' || c == '-') return 1;
        else if(c == '/' || c == '*') return 2;
        return 0;
    }

    

 double mathOperator(double operand1, double operand2, char Operator){
        switch(Operator){
            case '+': {
                return operand1 + operand2;
            }
            case '-' : {
                return operand1 - operand2;
            }
            case '*' : {
                return operand1 * operand2;
            }
            case '/' : {
                return operand1 / operand2;
            }
        }
        return 0;
    }
    
```

![Terrific Gogo (1)](https://user-images.githubusercontent.com/61011535/147389327-de670186-6805-4bda-8d14-4844c89f5ac4.png)

