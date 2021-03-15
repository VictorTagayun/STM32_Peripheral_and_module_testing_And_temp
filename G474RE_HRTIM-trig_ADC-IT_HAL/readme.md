# HRTIM will trigger ADC by Regular and Injected conversion and Interrupt after ADC conversion

## Setup

### Master HRTIM  

* Setup Master HRTIM with 50% duty using CMP1  
* ADC trigger1 on Master Period  
* ADC trigger2 on Master CMP1  
* setup last in main.c  

	if(HAL_OK != HAL_HRTIM_WaveformCounterStart(&hhrtim1, HRTIM_TIMERID_MASTER))
	{
		Error_Handler();
	}
	
### ADC  

* Setup 1 Regular Conversion mode   
	* External trigger by HRTIM trig 1 event
* Setup 1 Injected Conversion mode   
	* External trigger by HRTIM trig 2 event
* Enable NVIC Global IT with Call HAL handler
* Add callback for Regular Conversion mode  

	HAL_ADC_ConvCpltCallback(ADC_HandleTypeDef *hadc)
	{
	  /* Prevent unused argument(s) compilation warning */
	  UNUSED(hadc);

	  /* NOTE : This function should not be modified. When the callback is needed,
				function HAL_ADC_ConvCpltCallback must be implemented in the user file.
	   */

	//  HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, SET);
	  HAL_GPIO_WritePin(GPIOC, GPIO_PIN_8, SET);
	  adc_data = HAL_ADC_GetValue(hadc);
	}

* Add callback for Injected Conversion mode  

	HAL_ADCEx_InjectedConvCpltCallback(ADC_HandleTypeDef *hadc)
	{
	  /* Prevent unused argument(s) compilation warning */
	  UNUSED(hadc);

	  /* NOTE : This function should not be modified. When the callback is needed,
				function HAL_ADCEx_InjectedConvCpltCallback must be implemented in the user file.
	  */
	//  HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, SET);
	  HAL_GPIO_WritePin(GPIOC, GPIO_PIN_8, RESET);
	  adc_data = HAL_ADCEx_InjectedGetValue(hadc, ADC_INJECTED_RANK_1);
	}
	
* Enable ADC and IT  

	/*##- Enable Injected ADC Channel IT ##############################*/
	if(HAL_OK != HAL_ADCEx_InjectedStart_IT(&hadc1))
	{
		/* Start Error */
		Error_Handler();
	}

	/*##- Enable Regular Injection ADC Channel ##############################*/
	if(HAL_OK != HAL_ADC_Start_IT(&hadc1))
	{
		/* Start Error */
		Error_Handler();
	}
	
