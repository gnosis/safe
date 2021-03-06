==================================
Replace Existing Browser Extension
==================================

========  ===========================  ================  ==========
epic      title                        author            created
========  ===========================  ================  ==========
`60`_     Replace Browser Extension    DmitryBespalov    2019-01-04
========  ===========================  ================  ==========

.. _60: https://github.com/gnosis/safe/issues/60


.. _Main:


#. `Main`_
#. `User Interface`_
#. `Technical Details`_

.. sectnum::
.. contents:: Table of Contents
    :local:
    :depth: 2

Problem Definition
---------------------

* Access to the current browser extension is lost.
* User deleted existing browser extension on the computer
  (maybe even deleted the browser) and installed it again.
* User has access to the current browser extension but wants
  to set up browser extension on another computer.

Inputs
-----------

* Recovery phrase

  - 12 words separated by spaces or newlines, punctuation is ignored.

* New browser extension’s QR code

  - A QR code image containing browser extension’s registration code.
  - QR code encodes a JSON structure.

Outputs
------------

* New “Replace Browser Extension” transaction
* Error messages

  - Contextual, shown where the error belongs (balance,
    recovery phrase input)
  - Pop-ups (alerts), shown when some step in the process fails


Use Case Scenarios
-----------------------

Happy Case
~~~~~~~~~~~~~~~~~

Preconditions
+++++++++++++

* Existing Safe with connected browser extension.
* The Safe has enough funds for the transaction.
* Backend services are reachable and working.
* New browser extension installation, ready to connect with the Safe.
* User has valid recovery phrase for the Safe.

Steps
+++++

1. Open the app on the main screen.

2. Select the "Menu" action. The "Menu" screen opens.

3. Select "Replace Browser Extension" item.
   The "Intro" [`iOS <ios_intro_>`_] screen
   opens. The screen displays current Safe balance, estimated
   transaction fee and resulting future balance after transaction execution.

.. _happy_4:

4. Select "Start". The "Scan QR Code" [`iOS <ios_scan_>`_] screen opens.

5. Select "Scan". The "Camera" screen opens.

6. Point camera to and scan the QR code displayed in the new Browser Extension.
   "Camera" screen closes. "Recovery Phrase"
   [`iOS <ios_phrase_>`_] screen opens.

7. Type in the recovery phrase.

8. Select "Next". The "Review" [`iOS <ios_review_>`_] screen opens.

.. _`step No.9.`:

9. Select "Submit". The screen closes and "Transaction List"
   [`iOS <ios_list_>`_] screen shown.
   There is new transaction in the list - "Replace Browser Extension"
   (**NOTE:** the password or biometric authentication is not requested because
   the recovery phrase was provided).

.. _`step No.10.`:

10. Select that pending transaction.

.. _`step No.11.`:

11. The "Transaction Details" [`iOS <ios_details_>`_]
    screen opens with details about the transaction.

.. _happy_post:

Postconditions
++++++++++++++

* The Safe balance is updated with deducted
  funds according to the cost of replace transaction
  when the transaction is processed.
* There is a new "Replace Browser Extension" transaction
  in the transactions list.
* The new browser extension is connected to the Safe and displays
  its information.
* The old browser extension is now disconnected
  (push connection, and blockchain)
  from the Safe and doesn't have Safe's information.


Insufficient Funds
~~~~~~~~~~~~~~~~~~~~~~~~~

Inherits from the `Happy Case`_

Inherits from
    means that this scenario takes all the preconditions,
    steps and postconditions from another use case and possibly extends
    or overrides them with new values.

Preconditions
+++++++++++++

* The Safe has less funds than required for the transaction execution.

Steps
+++++

3. 1. The "Start" action is deactivated. The screen shows
      "Insufficient balance" message. User cannot proceed with the replacement.

.. _post_no_change:

Postconditions
++++++++++++++

* No new transaction is created.
* Old extension is still connected.
* New extension is not connected.
* Safe balance is not changed.

Updated Balance after Insufficient Funds
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Inherits from the `Insufficient Funds`_

Steps
+++++
3. 2. While staying in the Intro screen, the Safe balance is updated
      (say, new incoming transfer was received to the Safe account).
      The new balance is enough for making the transaction.
   3. Error message disappears. "Start" action becomes active.
      Proceeding according to `Happy Case No. 4 <happy_4_>`_

Postconditions
++++++++++++++
* Same as in `Happy Case <happy_post_>`_

Invalid QR Code
~~~~~~~~~~~~~~~~~~~~~~

Inherits from the `Happy Case`_

Steps
+++++

6. Point the camera and scan arbitrary QR-code with wrong data.
   The error alert shows up explaining that the scanned QR-code
   is invalid. After alert dismissal the camera screen stays open.

Existing Extension Scanned
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Inherits from the `Happy Case`_

Steps
+++++

6. Point the camera and scan existing extension's QR-code.
   The error alert shows up explaining that the QR-code must
   be from new extension.After alert dismissal the camera screen stays open.

Invalid Recovery Phrase
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Inherits from the `Happy Case`_

Steps
+++++

.. _existing_extension:

7. Type in arbitrary text.

8. Select "Next". The error alert shows up explaining that
   the recovery phrase is invalid. User must enter valid phrase again,
   starting from the `step No.7 <existing_extension_>`_.

Recovery Owners Not Found
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Inherits from the `Happy Case`_

Steps
+++++

.. _owners_not_found:

7. Type arbitrary recovery phrase that derives owners that
   are not existing in the Safe contract's owner list.

8. Select "Next". The error alert shows up explaining that the recovery
   phrase is not valid for this Safe. User must enter valid phrase again,
   starting from the `step No.7 <owners_not_found_>`_.

Backend Services Unreachable
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Inherits from the `Happy Case`_

Preconditions
+++++++++++++

* No Internet connection or backend services unreachable
  (infura, relay, notification services) or become unreachable
  at any step of the process.

Steps
+++++

3. 1. "Intro" screen opens, the estimation of transaction costs,
      and resulting balance are not shown. Current balance might be
      shown if this is known from previous balance updates.

      * "Start" action is disabled.
      * There is an error message showing that
        "no internet connection" available.
      * When backend services become reachable again and the Intro
        screen is still open, then:

        - Transaction fee estimation is updated.
        - Balance and resulting balance is updated.
        - "Start" action is enabled.

.. _unavailable_alert:

6. 1. If services become unreachable, then after scanning the valid
      QR-code, the alert is shown
      an alert with error is shown explaining "no Internet connection".
      The "Camera" screen closes.

8. 1. If services become unreachable, then after selecting "Next",
      (see `step No. 6.1. <unavailable_alert_>`_).

9. 1. If services become unreachable, then after selecting "Submit"
      on the "Review" screen, the alert is shown
      (see `step No. 6.1. <unavailable_alert_>`_).
      The "Review" screen stays open.

Cancellation
~~~~~~~~~~~~~~~~~~~

Inherits from the `Happy Case`_

Steps
+++++

4. Select "Cancel" action. The "Intro" screen hides.
   The "Menu" screen is shown.

9. Select "Cancel" action. The "Review" screen hides.
   The "Menu" screen is shown.

Backend Service Errors
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Inherits from the `Happy Case`_

Possible errors may appear in various requests, by service:

* Infura

  - Safe balance update
  - get Safe owners
  - get Safe trheshold
  - get replace transaction status

* Relay Service

  - estimate transaction
  - submit transaction

* Notification Service

  - delete old extension pair
  - create new extension pair
  - send "Safe connected" notification

.. _`case "A"`:

A. In case of an error happening as a result of opening the screen
   and executing network requests in the background:

* The action on that screen should be disabled.
  The error message should be displayed inline in the screen,
  i.e. without popping up an alert.

.. _`case "B"`:

B. In case of an error happening as a result of a user action on the screen:

* The action on that screen stays enabled. An alert shows up
  explaining the failure reason and next steps.

Steps
+++++
3. In case of request failures happening during screen loading state,
   follow `case "A"`_ from the above.

4. After selecting "Start", in case of errors,
   follow `case "B"`_ from the above.

6. After scanning the valid QR-code, in case of errors,
   follow `case "B"`_ from the above.

8. After selecting "Next", in case of errors,
   follow `case "B"`_ from the above.

9. After selecting "Submit", in case of errors,
   follow `case "B"`_ from the above.

Failed Transaction
~~~~~~~~~~~~~~~~~~~~~~~~~~

Inherits from the `Happy Case`_

Steps
+++++

10. 3. In case of transaction failure in the blockchain, the
       failed transaction shows its status in the transaction list.

Postconditions
++++++++++++++

* The "failed" transaction of type "Replace Browser Extension" is
  displayed in the transaction list.
* Old browser extension is still connected
  and is owner (push notification, blockhain).
* New browser extension is not connected.
* Safe balance is updated with regard to executed transaction costs.

Contract Validation Errors
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Inherits from the `Happy Case`_

Steps
+++++

3. 1. 1. If during screen loading, the fetched Safe's contract
         master copy address is not in the list of valid recognized
         master copy addresses, then show an alert explaining the error.
         After closing the alert, close the "Intro" screen.
         Show the "Menu" screen.

      2. If during screen loading, the fetched Safe's signature
         threshold is greater than the expected number of derived owners
         from the recovery phrase (owner count < required signature count),
         then this Safe setup scheme is unsupported. Show an alert explaining
         the error. After closing the alert, close the "Intro" screen.
         Show the "Menu" screen.

Postconditions
++++++++++++++

* `Nothing changed <post_no_change_>`_

Slow Operating Conditions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Inherits from the `Happy Case`_

Preconditions
+++++++++++++

* Network requests may take long time to execute.
* Database or other underlying operations are taking long time to execute.
* Time threshold = 100 milliseconds.
* Network request timeout time = 30 seconds.

  - Network request timeouts are handled as
    network errors (see `Backend Service Errors`_).

Steps
+++++

3. 1. If "Intro" screen loading time exceeds the time threshold,
      then indicate loading on the screen. Display placeholder values
      instead (balance, transaction fee and so on).

- The "Start" action should not be available during loading.

6. 1. If after scanning valid code the operation time exceeds
      the time threshold, then show the spinner. Disable the "Scan" action.

8. 1. If after "Next" the operation time exceeds the time threshold,
      then show the spinner. Disable the "Next" action.

- In all of the cases above, if spinner is shown, there must be a
  way to cancel the running operation. "Cancel" action aborts the
  current operation and aborts the use case.
  In case the operation is mutating
  (connecting new extension), then the opposite operation must be
  executed in the background after user cancellation action
  (disconnecting newly connected extension).

Repeated Actions
~~~~~~~~~~~~~~~~~~~~~~~~

Inherits from the `Happy Case`_

The idea is that once an action is selected,
it cannot be selected again until it is executed.

Steps
+++++

.. _`No. 4.1.`:

4. 1. Selecting "Start" while the action is running should
      not be possible. (Selecting "Start" disables the "Start" action).
      In case of error, the action should be re-enabled.

5. 1. Selecting "Scan", behavior is similar to `No. 4.1.`_

6. 1. Scanning valid code, the "Scan" action behavior is similar to `No. 4.1.`_

8. 1. Selecting "Next", behavior is similar to `No. 4.1.`_

9. 1. Selecting "Submit", behavior is similar to `No. 4.1.`_

Session Expiration
~~~~~~~~~~~~~~~~~~~~~~~~~~

Inherits from the `Happy Case`_

Preconditions
+++++++++++++

* The app is minimized and the usage session is expired

Steps
+++++

1. 1. Open the app. Before the main screen is displayed,
      the "Unlock" screen shows up requiring unlocking the app.

In-between App Termination
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Inherits from the `Happy Case`_

Preconditions
+++++++++++++

* The app is force-closed during any of the steps.

Steps
+++++

* Closing the app in any of the steps before `step No.9.`_ results
  in fresh start, i.e. opening of the main screen.
* In case app was force-closed after or during the `step No.9.`_,
  submitting the transaction, the transaction displayed in the list.

Postconditions
++++++++++++++
* If the app was forced-closed before the `step No.9.`_:
  see `Insufficient Funds Postconditions <post_no_change_>`_
* Otherwise, see `Happy Case Postconditions <happy_post_>`_

Multiple QR-codes
~~~~~~~~~~~~~~~~~~~~~~~~~

Inherits from the `Happy Case`_

Steps
+++++

8. 1. In case multiple QR-codes recognized in the same camera
      viewport, then use the first valid QR code.

.. _`User Interface`: 02_user_interface.rst
.. _`Technical Details`: 03_technical_details.rst
.. _`About Use Case Scenarios`: ../common/about_use_case_scenarios.rst
.. _ios_intro: 02_user_interface.rst#1-intro
.. _ios_intro_funds_error: 02_user_interface.rst#intro-insufficient-funds
.. _ios_scan: 02_user_interface.rst#scan-qr-code
.. _ios_phrase: 02_user_interface.rst#recovery-phrase
.. _ios_review: 02_user_interface.rst#5-review
.. _ios_list: 02_user_interface.rst#transaction-list
.. _ios_details: 02_user_interface.rst#transaction-details
