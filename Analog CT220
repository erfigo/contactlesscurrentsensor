#include <msp430.h> 
#include <stdio.h>
#include <stdint.h>
//FIXED ANALOG READING CODE FOR CT220
#define ADCSub 10

int i;
volatile int8_t j;
uint16_t pos;
unsigned int index;
uint16_t Avr;
char moving[100];
float voltage, current;

volatile uint16_t ADCBuff[ADCSub];
volatile uint16_t Sum;
volatile uint16_t ADCLast;
volatile uint8_t flag;
uint16_t ADCValue;

int movingAvg(volatile uint16_t *ptrArrNumbers,volatile uint16_t *ptrSum, uint16_t pos, uint16_t len, uint16_t nextNum)
{
  //Subtract the oldest number from the prev sum, add the new number
  *ptrSum = *ptrSum - ptrArrNumbers[pos] + nextNum;
  //Assign the nextNum to the position in the array
  ptrArrNumbers[pos] = nextNum;
  //return the average
  return *ptrSum / len;
}

int main(void)
 {
    j = 0;
    ADCValue = 0;
    Sum = 0;
    flag =0;

    //WDTCTL = WDTPW | WDTHOLD; // stop watchdog timer
    WDTCTL = WDT_ADLY_1000;                 // Set Watchdog Timer interval to ~1000ms
    SFRIE1 |= WDTIE;                            // Enable WDT interrupt

    // Initialize UART
    UCA1CTLW0 |= UCSWRST; // Put UART in reset state
    UCA1CTLW0 |= UCSSEL__SMCLK; // Clock source: SMCLK (16 MHz)
    UCA1BRW = 8; // Baud rate: 115200
    UCA1MCTLW = 0XD600;

    P1DIR |= BIT0;//LED
    P1OUT &= ~BIT0;//Turn off initially

    P1SEL1 |= BIT2;//analog function for P1.2
    P1SEL0 |= BIT2;

    P4SEL1 &= ~BIT3; //uart TX Function; clear bit P4.3
    P4SEL0 |= BIT3; // Set bit P4.3

    PM5CTL0 &= ~LOCKLPM5;
    UCA1CTLW0 &= ~UCSWRST; // Initialize USCI state machine

    ADCCTL0 &= ~ADCSHT;
    ADCCTL0 |= ADCSHT_2;
    ADCCTL0 |= ADCON;

    ADCCTL1 |= ADCSSEL_2;
    ADCCTL1 |= ADCSHP;

    ADCCTL2 &= ~ADCRES;
    ADCCTL2 |= ADCRES_2;

    ADCMCTL0 |= ADCINCH_2;
    ADCIE |= ADCIE0;// coversion complete interrupt, local enable
    __enable_interrupt();
    ADCCTL0 |= ADCENC|ADCSC;

    for ( j=0; j<ADCSub; j++)
        Sum += ADCBuff[j];
  //  ADCValue = Sum / ADCSub;


    while(1)
    {
        Avr = movingAvg(ADCBuff, &Sum, pos, ADCSub, ADCLast);
      //  adcResult = (Avr/1000)
        pos++;
        if (pos >= ADCSub)
            pos =0;

      //current = (Avr - 2921.0)/-19.7; //Taken from experiments with cable at gap 1 cm
      //current = (Avr - 2890.8)/-12.036;
        current = (Avr - 2908.8)/-12.036;//Taken from experiment with cable at gap 1,5 cm (updated)
      //current = (Avr - 2951.88)/-11.84;//Taken from experiment with cable at gap 1,5 cm (average of multiple experiments) //(alternative when ADC value at 0 A is around 2950)
       //current = (Avr - 3011.3)/-11.714;//Taken from 3 back-to-back experiments with Vdd= 5.03 V at gap 1.5 cm
       //current = (Avr - 3021.0)/-14.3395; //Taken from gap 1.5 cm
      // current = (Avr - 3039.5)/-15.943; //Taken from gap 1.5 cm

        voltage = ((Avr)/4095.0) *3.3;

          sprintf(moving, "The current detected: %.3lf A\r\n"
                  "The voltage output detected: %.2f V\r\n"
                  "The ADC Value: %d\r\n\n", current, voltage, Avr);
             for(index=0; index<sizeof(moving); index++)
                    {
                       UCA1TXBUF= moving[index];
                       for(i=0;i<100;i++){};
                    }
           __delay_cycles(100000);
            ADCCTL0 |= ADCENC|ADCSC;
    }
}

//--------------------------------------------------
//-----ISR

#pragma vector = ADC_VECTOR
__interrupt void ADC_ISR(void){

    // Convert ADC result to voltage (in millivolts)
if (flag == 0)
{if (j> (ADCSub-1))
{
    flag =1;
    PM5CTL0 &= ~LOCKLPM5;

}
    ADCBuff[(uint8_t)j] = ADCMEM0;
   j++;}

else
{
    ADCLast = ADCMEM0;
    PM5CTL0 &= ~LOCKLPM5;

}

}

// Watchdog Timer interrupt service routine
#if defined(__TI_COMPILER_VERSION__) || defined(__IAR_SYSTEMS_ICC__)
#pragma vector=WDT_VECTOR
__interrupt void WDT_ISR(void)
#elif defined(__GNUC__)
void __attribute__ ((interrupt(WDT_VECTOR))) WDT_ISR (void)
#else
#error Compiler not supported!
#endif
{
    P1OUT ^= BIT0;                          // TBeloggle P1.0 (LED)
}

