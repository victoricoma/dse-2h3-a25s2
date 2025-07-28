# 🪴 Esquema de Funcionamento da Horta Automatizada com Arduino [Professor Victor Icoma]
![projeto agua](https://github.com/user-attachments/assets/a9407581-8981-4162-bdef-06129cdf7ff2)

### **Objetivo Geral**

Automatizar o controle de luminosidade e irrigação de uma horta de hortaliças utilizando Arduino, sensores e servo motores. A ideia é que a persiana feche quando o sol estiver muito forte, e a água seja liberada em momentos ideais, simulando uma irrigação inteligente e simples.

---

### **Componentes Utilizados**

* 1x **Arduino Uno**
* 1x **Sensor LDR** (Sensor de luminosidade)
* 2x **Servos Motores** (SG90 ou similar)

  * Um para **abrir/fechar persiana**
  * Um para **abrir/fechar comporta de irrigação**
* 1x **Mini-bomba d’água** *(opcional, se quiser forçar pressão de água)*
* 1x **Fonte de água** (reservatório)
* **Fios jumper**, **protoboard**, **resistor de 10k** para o LDR
* **Estrutura física com arame** e **canaletas** para abrir as persianas e levantar a comporta d'água

---

### **Funcionamento Lógico**

#### 🌞 1. Controle da Persiana com LDR

* O sensor LDR mede a luminosidade ambiente.
* Se a luz for **maior que um limite** (ex: valor analógico > 800):

  * O **servo motor gira a persiana** para **fechá-la** (proteção contra sol intenso).
* Se a luz for **abaixo do limite**:

  * O servo **abre a persiana** novamente.

#### 💧 2. Controle de Irrigação com Servo

* A irrigação é ativada de forma **automática por tempo** ou por um **botão de controle manual**.
* O servo **abre a comporta** de entrada de água, permitindo que a água escorra sobre as hortaliças por gravidade (simulado com arame puxando uma válvula).
* Após um tempo determinado (ex: 10 segundos), o servo fecha novamente a comporta.

---

### **Fluxo Simplificado**

```plaintext
[Início do Loop]
    |
    ├── Lê valor do LDR
    │     ├── Se LUZ > LIMITE → Fechar Persiana
    │     └── Se LUZ ≤ LIMITE → Abrir Persiana
    |
    ├── Verifica tempo ou botão para irrigação
    │     ├── Ativa servo → Abre comporta de água
    │     └── Aguarda 10 segundos → Fecha comporta
    |
[Fim do Loop]
```

---

### **Exemplo de Código Arduino (Simplificado)**

```cpp
#include <Servo.h>

Servo persiana;
Servo comportaAgua;

const int LDR_PIN = A0;
const int LDR_LIMITE = 800;

void setup() {
  persiana.attach(9);         // Pino digital para persiana
  comportaAgua.attach(10);    // Pino digital para água
  pinMode(LDR_PIN, INPUT);
}

void loop() {
  int valorLuz = analogRead(LDR_PIN);

  if (valorLuz > LDR_LIMITE) {
    persiana.write(0);     // Fecha persiana
  } else {
    persiana.write(90);    // Abre persiana
  }

  // Simulação de irrigação por tempo
  static unsigned long ultimaIrrigacao = 0;
  if (millis() - ultimaIrrigacao > 60000) { // a cada 60s
    comportaAgua.write(90); // Abre comporta
    delay(10000);           // Irriga por 10 segundos
    comportaAgua.write(0);  // Fecha comporta
    ultimaIrrigacao = millis();
  }
}
```
