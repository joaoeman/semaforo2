. Inicialização e Definições
O código começa incluindo as bibliotecas necessárias para controlar o hardware do Raspberry Pi Pico, como pico/stdlib.h e pico/time.h.

São definidos os pinos GPIO para cada LED e para o botão:


const uint LED_A = 11; // LED no GPIO 11
const uint LED_B = 12; // LED no GPIO 12
const uint LED_C = 13; // LED no GPIO 13
const uint BUTTON_PIN = 5; // Botão no GPIO 5
Variáveis globais são declaradas para controlar o estado dos LEDs e o tempo de desligamento.

2. Função turn_off_callback
Esta função é chamada por um alarme após 3 segundos para desligar os LEDs.

Ela desliga os LEDs em sequência, com um intervalo de 3 segundos entre cada um:

gpio_put(LED_A, false); // Desliga o LED A
sleep_ms(3000); // Espera 3 segundos
gpio_put(LED_B, false); // Desliga o LED B
sleep_ms(3000); // Espera 3 segundos
gpio_put(LED_C, false); // Desliga o LED C
A variável led_active é atualizada para false, indicando que os LEDs estão desligados.

3. Função main
A função main é o ponto de entrada do programa.

Inicializa a comunicação serial e configura os pinos GPIO:


stdio_init_all(); // Inicializa a comunicação serial
gpio_init(LED_A); // Inicializa o pino do LED A
gpio_set_dir(LED_A, GPIO_OUT); // Configura o pino como saída
gpio_init(LED_B); // Inicializa o pino do LED B
gpio_set_dir(LED_B, GPIO_OUT); // Configura o pino como saída
gpio_init(LED_C); // Inicializa o pino do LED C
gpio_set_dir(LED_C, GPIO_OUT); // Configura o pino como saída
gpio_init(BUTTON_PIN); // Inicializa o pino do botão
gpio_set_dir(BUTTON_PIN, GPIO_IN); // Configura o pino como entrada
gpio_pull_up(BUTTON_PIN); // Habilita o resistor de pull-up no pino do botão
4. Loop Principal
O programa entra em um loop infinito (while (true)), onde verifica continuamente o estado do botão:


if (gpio_get(BUTTON_PIN) == 0 && !led_active) { // Verifica se o botão foi pressionado
    sleep_ms(50); // Debounce para evitar leituras errôneas
    if (gpio_get(BUTTON_PIN) == 0) { // Verifica novamente o estado do botão
        gpio_put(LED_A, true); // Acende o LED A
        gpio_put(LED_B, true); // Acende o LED B
        gpio_put(LED_C, true); // Acende o LED C
        led_active = true; // Atualiza o estado para indicar que os LEDs estão acesos
        add_alarm_in_ms(3000, turn_off_callback, NULL, false); // Configura o alarme para desligar os LEDs após 3 segundos
    }
}
sleep_ms(10); // Pequena pausa para reduzir o uso da CPU

5. Resumo do Funcionamento
Quando o botão é pressionado, os três LEDs são acesos.

Após 3 segundos, a função turn_off_callback é chamada, desligando os LEDs em sequência, com um intervalo de 3 segundos entre cada um.

O loop principal continua verificando o estado do botão, permitindo que o processo se repita quando o botão for pressionado novamente.

