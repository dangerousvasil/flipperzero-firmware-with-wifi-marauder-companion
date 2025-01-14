# Unit tests
## Intro
Unit tests are special pieces of code that apply known inputs to the feature code and check the results to see if they were correct. 
They are crucial for writing robust, bug-free code.

Flipper Zero firmware includes a separate application called [unit_tests](/applications/debug/unit_tests).
It is run directly on the Flipper Zero in order to employ its hardware features and to rule out any platform-related differences.

When contributing code to the Flipper Zero firmware, it is highly desirable to supply unit tests along with the proposed features. 
Running existing unit tests is useful to ensure that the new code doesn't introduce any regressions.

## Running unit tests
In order to run the unit tests, follow these steps:
1. Compile the firmware with the tests enabled: `./fbt FIRMWARE_APP_SET=unit_tests`.
2. Flash the firmware using your preferred method.
3. Copy the [assets/unit_tests](assets/unit_tests) folder to the root your Flipper Zero's SD card.
4. Launch the CLI session and run the `unit_tests` command.

**NOTE:** To run a particular test (and skip all others), specify its name as the command argument. 
See [test_index.c](applications/debug/unit_tests/test_index.c) for the complete list of test names.

## Adding unit tests
### General
#### Entry point
The common entry point for all tests it the [unit_tests](applications/debug/unit_tests) application. Test-specific code is placed into an arbitrarily named subdirectory and is then called from the [test_index.c](applications/debug/unit_tests/test_index.c) source file.
#### Test assets
Some unit tests require external data in order to function. These files (commonly called assets) reside in the [assets/unit_tests](/assets/unit_tests) directory in their respective subdirectories. Asset files can be of any type (plain text, FlipperFormat(FFF), binary etc).
### Application-specific
#### Infrared
Each infrared protocol has a corresponding set of unit tests, so it makes sense to implement one when adding support for a new protocol.
In order to add unit tests for your protocol, follow these steps:
1. Create a file named `test_<your_protocol_name>.irtest` in the [assets](assets/unit_tests/infrared) directory.
2. Fill it with the test data (more on it below).
3. Add the test code to [infrared_test.c](applications/debug/unit_tests/infrared/infrared_test.c).
4. Update the [assets](assets/unit_tests/infrared) on your Flipper Zero and run the tests to see if they pass.

##### Test data format
Each unit test has 3 sections: 
1. `decoder` - takes in raw signal and outputs decoded messages.
2. `encoder` - takes in decoded messages and outputs raw signal.
3. `encoder_decoder` - takes in decoded messages, turns them into raw signal and then decodes again. 

Infrared test asset files have an `.irtest` extension and are regular `.ir` files with a few additions.
Decoder input data has signal names `decoder_input_N`, where N is a test sequence number. Expected data goes under the name `decoder_expected_N`. When testing the encoder these two are switched.

Decoded data is represented in arrays (since a single raw signal may decode to several messages). If there is only one signal, then it has to be an array of size 1. Use the existing files as syntax examples.

##### Getting raw signals
Recording raw IR signals is possible using Flipper Zero. Launch the CLI session, run `ir rx raw`, then point the remote towards Flipper's receiver and send the signals. The raw signal data will be printed to the console in a convenient format.
