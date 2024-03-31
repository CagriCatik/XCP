# Universal Measurement and Calibration Protocol

The XCP protocol is used for measuring and calibrating ECUs (reading and writing data from the internal memory). XCP was designed to offer measurement data acquisition with minimal slave resource consumption not limited to a specific transport layer. It can be used in CAN, Ethernet, Flexray networks.

Learning goals:
At the end of the learning module you will...

...know about the most important use cases of XCP.

...know the structure of XCP messages.

...know the different measurement modes available in XCP.

...know about the Vector VX1000 solution for high performance measurement data acquisition using XCP. 

Structure:
The learning module is structured in four chapters. Using the tab bar, you can browse through the different chapters. Each chapter contains a description of the covered topics, an animated learning video as well as a summary.

## Introduction to XCP

In the first chapter you will learn about the use cases behind XCP. What does it mean to measure data from an ECU? How do you calibrate an ECU using XCP?

You will learn about the structure of XCP messages – requests and responses. You will learn how XCP messages are sent over an existing communication network, e.g. a CAN bus.

XCP requests, like the SHORT_UPLOAD command, can be used to read specific cells of the internal memory of an ECU. These commands will be used to measure signals directly from the internal memory, as you will learn in the second chapter. On the other hand, data can also be written back into the internal memory, e.g. by using a DOWNLOAD command.

XCP connections are point-to-point connections between a Master (e.g. a measurement and calibration tool like CANape) and a Slave (the XCP driver of an ECU). Communication is always established by the Master. The Master will send a XCP command inside the Data Field of a transport layer frame of the existing network. The type of command is transferred in the first byte of the XCP message as a Package Identifier (PID). The following bytes of the message contain information needed by the slave to execute the command. E.g. an address and the number of memory cells to be read out by the slave. The XCP slave answers either with a positive response or a negative response. A positive response may contain additional data, e.g. the requested information. A negative response will carry an error code.

The PIDs and structure of XCP commands and their responses are described in ASAM’s specification of the XCP protocol. The protocol also describes the structure of XCP frames in a CAN, a FlexRay, a Ethernet network etc. Measurement and calibration tasks will be implemented using the same XCP commands in different transport layers. Simply the transport layer frames carrying the corresponding XCP commands will change.

In the case of XCP on CAN, each Master-Slave-pair will communicate with a fixed set of IDs, e.g. CANape will use the CAN-ID 200 and the ECU the CAN-ID 201 for their XCP communication. In case a second Master-Slave-pair exists, a different set of fixed CAN-IDs will be used.

In the next chapter you will learn about how XCP commands are used to provide cyclical measurement modes.

## Polling

XCP offers two different measurement modes, the Polling mode and the Synchronous Data Aquisition mode – in short DAQ mode.

The CANape user decides about the measurement mode in CANape in the Measurement Configuration.

In this chapter you will learn about the Polling mode and its communication structure. At the end of the chapter, we will discuss the advantages and disadvantages of this measurement mode.

The Polling mode is implemented using SHORT_UPLOAD or UPLOAD commands. Each requested signal will be read from the ECU’s memory using a separate command. XCP commands are always processed sequentially meaning that first a command has to be processed and acknowledged before a following command can be send by the Master.

As a consequence, it is not possible to measure several signals exactly at the same time using the Polling mode. Signals are measured asynchronously with a time delay in between.

Measuring many signals in the Polling mode will lead to a significantly high bus load, since at least two XCP messages are needed in each cycle per measured signal.

In conclusion, the Polling mode is only suited for measuring less-dynamic signals at lower measurement rates, e.g. temperature variables. If you are wondering in which other scenarios the Polling mode turns out to be useful, feel free to ask this question the trainer of your upcoming CANape training.

In order to measure signals synchronized – meaning measured exactly at the same time, the DAQ mode has to be used. In the next chapter, you will learn about this second, more elaborate measurement mode.

## Synchronous Data Aquisition

In this chapter you will learn about the most important feature of the XCP protocol - measuring data synchronously using the DAQ Mode.

We will have a look at the different phases as well as the communication structure of the DAQ mode. We will compare the DAQ mode to the already known Polling mode. In short, we will discuss how different DAQ measurement modes with different measuring rates are implemented and how to decide which rates shall be used for measuring our signals. The DAQ mode is implemented using XCP events added to the software of an ECU. Each XCP event implements a single DAQ measurement mode. Since the XCP event will be called cyclically at a fixed rate in the ECU’s software, the corresponding DAQ measurement mode will also have a fixed rate.

Once a XCP event is called, data will be measured – synchronously – and sent to the XCP master. Therefore, during the measurement the XCP master will no longer need to request any data. Only the XCP slave will sent XCP messages. This leads to a significantly lower busload compared to the polling mode.

Signals should always be measured using a DAQ mode measuring at the same rate as the requested signals are updated in the ECU. Not at a slower rate (we will miss data), nor at a faster rate (we will create additional busload without getting any more information). Therefore, it is important to find out about the perfect DAQ measurement mode of your signals prior to your measurement.

Besides cyclic XCP events (events called e.g. each 20 ms), also acyclic XCP events can be implemented. These XCP events will be triggered once a certain condition (e.g. shaftcrank reaches a certain position) is fulfilled. They can be used for measuring signals which are updated in the ECU on the same conditions.

If you are wondering about the best way how to store the information which signal shall be measured using which DAQ measurement mode, the trainer of your upcoming CANape training will know the answer.

## Performance Analysis

In this chapter you will learn about the two limiting factors of your DAQ measurement. We will have a look at the VX1000 system providing high data throughput and fast measurement rates to overcome these limitations. A DAQ measurement is always limited by the available bandwidth of the existing transport layer. In case of a CAN network (XCP on CAN), depending on your measurement task, this can be a very limiting factor. Also, the computing power assigned to your XCP slave, will limit the measurement rates and therefore the performance of your measurement.

With a Vector VX1000 system data throughput of several 100 Mbyte/s and measurement rates faster than 10 µs will be possible.

 