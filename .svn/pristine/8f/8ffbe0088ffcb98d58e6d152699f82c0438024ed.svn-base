#include "timer.h"
#include <platform.h>
#include "lcd.h"
#include <gpio.h>
#include <stdio.h>
#include <stdlib.h>
#include "leds.h"
#include "switches.h"
#include "lpc_eeprom.h"
#include "menu.h"

#define sensorIn P0_24 //16

#define PAGE_OFFSET         0x10
#define PAGE_ADDR           0x01

int time;
int score;
int updateScreen;

/*! \brief Increments the score and sets the updateScreen flag to true.
 *  
 *	Triggered when the sensor generates an interrupt.
 */
void increment_score();


/*! \brief Decrements the timer and sets the updateScreen flag to true.
 *  
 *	Triggered when the timer generates an interrupt.
 */
void decrement_timer();

/*! \brief The main loop of the program where the actual game is played.
 *  
 *	Loop breaks when timer hits 0.
 */
void gameplay_loop();

/*! \brief Takes a number and converts it to its ASCII equivalent.
 *  
 *	\param num The number to convert to ASCII.
 *	\return A character array containing the ASCII values of the digits of the number.
 */
char* convert_to_ascii(int num);

/*! \brief Returns the ASCII equivalent for a single digit.
 *  
 *	\param num The digit to convert to ASCII.
 * 	\return The ASCII equivalent of the digit.
 *
 *	Used by convert_to_ascii().
 */
int ascii_table(int num);

/*! \brief Saves the score at the end of the game to EEPROM
 * 
 */
void save_score();


void start_game(int timer){
	time = timer;
	score = 0;
	
	lcd_clear();

	
	timer_init(120000000);
	timer_set_callback(decrement_timer);
	timer_enable();
	
  gpio_set_mode(sensorIn, Input);
  gpio_set_trigger(sensorIn, Rising);
	gpio_set_callback(sensorIn, increment_score);
	
	gameplay_loop();
}

void gameplay_loop(){
	char convertedScore[3]=" ";
	char convertedTimer[3]=" ";
	lcd_set_cursor(0, 0);
	lcd_print("Score: ");
	lcd_set_cursor(0, 1);
	lcd_print("Timer: ");
	updateScreen = 1;
	while(time != 0){
		leds_set(0,0,1);
		//if interrupt is raised, update Screen
		if(updateScreen){
			sprintf(convertedTimer, "%d",time);
			sprintf(convertedScore, "%d",score);
			lcd_set_cursor(9, 0);
			lcd_print("  ");
			lcd_set_cursor(9, 0);
			lcd_print(convertedScore);
			lcd_set_cursor(9, 1);
			lcd_print("  ");
			lcd_set_cursor(9, 1);
			lcd_print(convertedTimer);
			updateScreen = 0;
			leds_set(0,0,0);
		}
		
	}
	
	//End game
	lcd_clear();
	lcd_set_cursor(0, 0);
	lcd_print("FINISH");
	lcd_set_cursor(0, 1);
	lcd_print("Score: ");
	lcd_set_cursor(7, 1);
	lcd_print(convertedScore);
	leds_set(0,0,0);
	save_score();
	timer_disable();
	while(1){
		leds_set(1,0,0);
		if (switch_get(P_SW_UP)) break; // to pause before going to the main menu
	}
	leds_set(0,0,0);
	init_menu();
}

void increment_score(){
	score++;
	updateScreen = 1;
}

void decrement_timer(){
	time--;
	updateScreen = 1;
}

void save_score(){                     /* From  EEPROM demo */
    uint32_t i, j;
    uint8_t count;
    uint8_t error = 0;
		uint8_t read_buffer[EEPROM_PAGE_SIZE*2];
	  uint8_t write_buffer[16]="TOP:            ";
		
		char* convertedArray = convert_to_ascii(score); 
	
		write_buffer[6] = *convertedArray; //inputting the score in the string being saved to memory
		write_buffer[7] = *(convertedArray+1);
		
    EEPROM_Init();

    count = sizeof(write_buffer);
    count &= 0xFC;

    for(i = 0; i < EEPROM_PAGE_NUM; i++)
    {
        EEPROM_Erase(i);
    }
    for(i=0;i<EEPROM_PAGE_NUM;i++)
    {
        uint32_t *ptr = (uint32_t*)read_buffer;
        EEPROM_Read(0,i,(void*)read_buffer,MODE_32_BIT,EEPROM_PAGE_SIZE/4);
        for(j = 0; j < EEPROM_PAGE_SIZE/4; j++)
        {
            if(*ptr++ != 0)
            {;
                error = 1;
                break;
            }
        }
    }
    if(error){
				leds_set(0,0,1);
        while(1);}
		
		
    EEPROM_Write(PAGE_OFFSET,PAGE_ADDR,(void*)write_buffer,MODE_8_BIT,count/1);
}

char* convert_to_ascii(int num){
	char* returnArray = malloc(sizeof(char)*2);
	//Extracting the digits of the supplied number
	int t_digit = (int)(num/10); 
	int tens_digit = ascii_table(t_digit);
	int ones_digit;
	
	if(t_digit == 0){
		ones_digit = ascii_table(num);
	}
	else{
		ones_digit = ascii_table((int)(num - t_digit*10)); //getting the difference to extract the rightmost digit
	}
	
	*returnArray = tens_digit;
	*(returnArray+1) = ones_digit;
	
	return returnArray;
}

int ascii_table(int num){
	switch(num){
		case 0:return 48;break;
		case 1:return 49;break;
		case 2:return 50;break;
		case 3:return 51;break;
		case 4:return 52;break;
		case 5:return 53;break;
		case 6:return 54;break;
		case 7:return 55;break;
		case 8:return 56;break;
		case 9:return 57;break;
	}
}


