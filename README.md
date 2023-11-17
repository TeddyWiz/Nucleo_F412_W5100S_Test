# Nucleo_F412_W5100S_Test

## HardWare Set
- Nucleo-F412
- W5100S-shield

    <img src="./Image/HW_Set.jpg" width="60%" height="50%" title="HW Setting" alt="HW config"></img>

## Project Set
- PIN Config

    <img src="./Image/PinSet.png" width="60%" height="50%" title="PIN Config" alt="PIN config"></img>

- CLK Config

    <img src="./Image/CLK_Set.png" width="60%" height="50%" title="CLK Config" alt="CLK config"></img>

- GPIO Config

    <img src="./Image/GPIO_Set.png" width="60%" height="50%" title="GPIO Config" alt="GPIO config"></img>

- SPI Config
    - Parameter Settings

        <img src="./Image/SPI_Set_Para.png" width="60%" height="50%" title="SPI Parameter Config" alt="SPI Parameter config"></img>

    - DMA Settings

        <img src="./Image/SPI_Set_DMA.png" width="60%" height="50%" title="SPI DMA Config" alt="SPI DMA config"></img>


## Program Seq

- Program Diagram

    <img src="./Image/w5100_Test_diagram_seq.drawio.png" width="60%" height="50%" title="Program Diagram" alt="Program Diagram"></img>

- SPI Config Code
    <pre>
    <code>

    void csEnable(void)
    {
        HAL_GPIO_WritePin(SPI1_CS_GPIO_Port, SPI1_CS_Pin, GPIO_PIN_RESET);
    }

    void csDisable(void)
    {
        HAL_GPIO_WritePin(SPI1_CS_GPIO_Port, SPI1_CS_Pin, GPIO_PIN_SET);
    }

    void spiWriteByte(uint8_t tx)
    {
        uint8_t rx;
        while (HAL_SPI_GetState(&hspi1) != HAL_SPI_STATE_READY);
        HAL_SPI_TransmitReceive(&hspi1, &tx, &rx, 1, 10);
        while (HAL_SPI_GetState(&hspi1) == HAL_SPI_STATE_BUSY_TX);    
    }

    uint8_t spiReadByte(void)
    {
        uint8_t rx = 0, tx = 0xFF;
        while (HAL_SPI_GetState(&hspi1) != HAL_SPI_STATE_READY);
        HAL_SPI_TransmitReceive(&hspi1, &tx, &rx, 1, 10);
        while (HAL_SPI_GetState(&hspi1) == HAL_SPI_STATE_BUSY_RX);
        return rx;
    }

    static void wizchip_read_burst(uint8_t *pBuf, uint16_t len)
    {
        HAL_SPI_Receive_DMA(&hspi1, pBuf, len);
        while (HAL_DMA_GetState(hspi1.hdmarx) == HAL_DMA_STATE_BUSY);
        while (HAL_SPI_GetState(&hspi1) == HAL_SPI_STATE_BUSY_RX);    
        return;
    }

    static void wizchip_write_burst(uint8_t *pBuf, uint16_t len)
    {
        HAL_SPI_Transmit_DMA(&hspi1, pBuf, len);
        while (HAL_DMA_GetState(hspi1.hdmatx) == HAL_DMA_STATE_BUSY);
        while (HAL_SPI_GetState(&hspi1) == HAL_SPI_STATE_BUSY_TX);
        return;
    }
    </code>
    </pre>

## Test Config

<img src="./Image/w5100_Test_diagram_config.drawio.png" width="60%" height="50%" title="Test Config" alt="Test Config"></img>

## Result
- Program Log

    <img src="./Image/Prog_Log1.png" width="60%" height="50%" title="Program L" alt="SPI DMA config"></img>

- AX2

    <img src="./Image/AX2_Cap.png" width="60%" height="50%" title="Program L" alt="SPI DMA config"></img>

- WireShark

    <img src="./Image/wireshark_cap.png" width="60%" height="50%" title="Program L" alt="SPI DMA config"></img>

    When transmitting 41,820 bytes using UDP, with a TX buffer allocated at 8K and an SPI clock at 20 MHz utilizing DMA, the results indicated that each transmission took approximately 25 to 35 milliseconds.