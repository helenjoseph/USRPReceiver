# **Implementation of USRP Receiver Block in System Studio**

The objective of the project is to develop a prototyping platform with a combination of sensible hardware and software. The Software Defined Radio (SDR) used in this project is a Universal Software Radio Peripheral (USRP) N Series and the software used is System Studio.
The objective of the project is to:
1. Interface the USRP device with the host system.
2. Develop a USRP receiver block in System Studio that reads samples from USRP device.
3. To display and analyze the Power Spectral Density (PSD) and constellation plots of the received spectrum.

## **Getting started**
The USRP family of products are designed by Ettus Research for RF applications from DC to 6 GHz. For this project USRP Networking Series was used. [Installation instruction](http://files.ettus.com/manual/page_usrp2.html) provide detailed instruction to connect the USRP device to the host system and the [build guidelines](http://files.ettus.com/manual/page_build_guide.html) provide instruction to build and install USRP Hardware Driver (UHD) from source.
This project requires

## **Implementation**

The solution was implemented by writing C++ Wrapper files that links the USRP source code to the System Studio interface. The preliminary function of the wrapper file was to:
- initialize the device by setting the necessary parameters such as sampling rate, frequency, host format and wire format
- start the device
- read the I and Q samples
- stop the device.

The primitive model (prim_model) in system studio is a non hierarchical control model that is indicated as source text. It allows users to call predefined system studio functions and external functions. It comprises of the following sections:
1. **Header**: Defines the ports, parameters and their datatypes.
```
type_param T = complex<float>;
port out T outr;
```
2. **main_action**: Defines the signal processing functionalities. This section consists of read and write functions that take a port name as a parameter. Read causes the next sample to be read from the input port and Write causes samples to be written to the output port.
```
main_action
{
  read_value=u.stream(BUF_LENGTH, mybuffer);
  for(i=0; i<read_value; i+=2)
  {
    b_r=(float)((mybuffer[i  ]));
    b_im=(float)((mybuffer[i+1]));
    outr = complex(b_r,b_im);
    write(outr);
  }
}
```
3. **reset_action**: Defines the model functionality during startup or during reset mode
```
reset_action
{
  u.init(SAMPLE_RATE,FREQUENCY,GAIN,HOSTFMT,WIREFMT);
  u.start();
}
```
4. **catch_termination**: Defines functionalities when the model terminates
```
catch_termination
{
u.stop ();
 printf("\n device closed \n \n");
}
```
