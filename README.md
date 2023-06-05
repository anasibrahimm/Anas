void move_forward();
void move_backward();
char UART_RxChar(void);
void UART_Init(int x);
char bluetooth='x';

//void mymsDelay(unsigned int z);
int counter = 0;

void interrupt(){
         PORTD=0x00;
         delay_ms(5000);
         INTCON = INTCON & 0xFD;

}
void main() {
TRISD=0x00; //0000 0000
TRISB =0x01; //0000 0111
TRISC=0x80;
PORTB = 0x01; //0000 0001
PORTD= 0x00;
INTCON=0x90;
OPTION_REG = OPTION_REG | 0x40;
delay_ms(100);
UART_Init(9600);
//delay_ms(100);



       while(1)
       {
               bluetooth='k';
               bluetooth= UART_RxChar();
               delay_ms(70);
            if (bluetooth=='a')//0000 0010
            {
               if(counter % 2 ==0)
               {
                          move_forward();
                     //delay_ms(2000);
                          counter++;
               }
            }

            else if (bluetooth=='b')//0000 0100
            {
               if(counter % 2 ==1){
                       move_backward();
                       //delay_ms(2000);
                       counter++;
               }
            }

        }




}

void move_forward() {
   PORTD=0x50;   //0101 0000


       mymsDelay(150);
        PORTD=0X00;
        mymsDelay(100); // 1010 0101 >> both motors are on and moving forward*/
}

void move_backward() 
{
 PORTD=0xA0; //1010 0000

          mymsDelay(150);
          PORTD=0x00;
          mymsDelay(100); // 0101 1010 >> both motors are on and moving backwards
}

void UART_Init(int baudRate)
{
    TRISC=0x80;            // Configure Rx pin as input and Tx as output
    TXSTA=(1<<5);  // Asynchronous mode, 8-bit data & enable transmitter
    RCSTA=(1<<7) | (1<<4);  // Enable Serial Port and 8-bit continuous receive
    SPBRG = (8000000UL/(long)(64UL*baudRate))-1;      // baud rate @8Mhz Clock
}
char UART_RxChar()
{
    while(!(PIR1 & 0xDF));    // Wait till the data is received
    PIR1 = PIR1 & 0xDF;            // Clear receiver flag
    return(RCREG);     // Return the received data to calling function
}
void mymsDelay(unsigned int z){
 unsigned int Dcntr=0;
 while(Dcntr<z)
 z--;

}
