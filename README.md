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




void move_down();
void move_up();
char UART_RxChar(void);
void UART_Init(int x);
char bluetooth='x';
int top=1;
int bottom=0;
void mymsDelay(unsigned int z);

void Delay_us(unsigned int microseconds);
void Delay_ms(unsigned int milliseconds);
void pwm_init();
void set_servo_position(int degrees);
int pulse_width;
void interrupt()
{

         PORTC = PORTC & 0x80;

         // bluetooth='z';
         delay_ms(5000);
         INTCON = INTCON & 0xFD;


}

void main()
{
 TRISB =0x01; //0000 0001
 TRISC=0x80;  //1000 0000
 INTCON=0x90;
 OPTION_REG = OPTION_REG & 0xBF;
 PORTB = 0x00; //0000 00010
 PORTC= 0x80; // WAS 0x00

 delay_ms(100);
 UART_Init(9600);
 delay_ms(100);
 pwm_init();



       while(1)
       {
               bluetooth='k';
               bluetooth= UART_RxChar();
               delay_ms(70);

            if (bluetooth=='a')
            {
               if(bottom==0)
               {
                          move_down();
                     //delay_ms(2000);
                          top=0;
                          bluetooth ='k';
               }
            }

            else if (bluetooth=='b')
            {
               if(top==0){
                       move_up();
                       //delay_ms(2000);
                       bottom=0;
               }
            }

            if (bluetooth=='c')
            {
                        PORTC =PORTC & 0x80; // 0111 1111

                        bottom=1;

            }

            if (bluetooth=='d')
            {
                        PORTC =PORTC & 0x80 ;            //1011 1111
                        top=1;
            }


             //PORTC= PORTC &0xFB;
             if (bluetooth=='e')
             {
                set_servo_position(180);
                bluetooth='y';
             }

             else if (bluetooth=='f')
             {
                  set_servo_position(0);
                  bluetooth='y';
             }











        }





}



void move_down() {
   PORTC=0x81;   //0000 0001
   mymsDelay(50);

   mymsDelay(200);
       mymsDelay(150);
        PORTD=0X00;
        mymsDelay(100); // 1010 0101 >> both motors are on and moving forward*/
}

void move_up()
{
 PORTC=0x82; //0000 0010
   mymsDelay(50);
      mymsDelay(200);
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
    PIR1 = PIR1 & 0xDF;       // Clear receiver flag
    return(RCREG);     // Return the received data to calling function
}
void mymsDelay(unsigned int z)
{
 unsigned int Dcntr=0;
 while(Dcntr<z)
 z--;

}

void pwm_init()
{
    TRISC2_bit = 0; // Set RC2 pin as output for servo

    // Configure CCP1 module for PWM (for servo on RC2)
    CCP1M3_bit = 1;
    CCP1M2_bit = 1;
    CCP1M1_bit = 0;
    CCP1M0_bit = 0;
    CCP1X_bit = 0;
    CCP1Y_bit = 0;

    // Configure Timer2 for PWM
    T2CKPS0_bit = 1; // Set Timer2 prescaler to 16
    T2CKPS1_bit = 1;
    TMR2ON_bit = 1; // Enable Timer2
    PR2 = 249; // Set period register for 50Hz frequency
}
void set_servo_position(int degrees)
{
    // Check if degrees is within the acceptable range
    if(degrees >= -180 && degrees <= 180) {
        // If degrees is negative, it's assumed that the servo should return to the 0 position
        if(degrees < 0) {
            degrees = 0;
        }

        pulse_width = (degrees * 11) + 500; // Calculate pulse width (500 to 2400 for 0-180 degrees)
        CCPR1L = pulse_width >> 2; // Set CCPR1L register
        CCP1CON = (CCP1CON & 0xCF) | ((pulse_width & 0x03) << 4); // Set CCP1CON register
        Delay_ms(20); // Delay for the servo to reach the desired position
    }
}
void Delay_us(unsigned int microseconds)
{
    while (microseconds--) {
        delay_us(1);
    }
}
"crane code"
void Delay_ms(unsigned int milliseconds) {
    while (milliseconds--) {
        delay_ms(1);
    }
}
