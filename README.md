<!--
# license: Copyright © 2011-2019 MOLPay Sdn Bhd. All Rights Reserved. 
-->
# rms-mobile-xdk-android

<img src="https://user-images.githubusercontent.com/38641542/74424311-a9d64000-4e8c-11ea-8d80-d811cfe66972.jpg">

This is the complete and functional Razer Merchant Services Android payment module that is ready to be implemented into Android Studio through gradle from JCenter/Maven repository as a MOLPayXDK module. An example application project 
(MOLPayXdkExample) is provided for MOLPayXDK framework integration reference.

这是一个完整和实用的 Razer Merchant Services 安卓支付模块，可以通过 JCenter/Maven 存储库中的 gradle 实现到 Android Studio 以作为一个 MOLPayXDK 框架。在此提供了一个示例应用程序项目（MOLPayXdkExample）以作为 MOLPayXDK 框架整合的参考。

## Recommended configurations

    - Minimum Android SDK Version: 27 ++
    
    - Minimum Android API level: 19 ++
    
    - Minimum Android target version: Android 4.4
    
    - Minimum Android Studio Gradle: 3.1.2

## Installation

    Step 1 - Add compile 'com.molpay:molpay-mobile-xdk-android:<put latest release version here>' to dependencies in application build.gradle
    
    Step 2 - Add import com.molpay.molpayxdk.MOLPayActivity;
    
    Step 3 - Add the result callback function to get return results when the payment activity ended,
    
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data)
    {
        if (requestCode == MOLPayActivity.MOLPayXDK && resultCode == RESULT_OK){
            Log.d(MOLPayActivity.MOLPAY, "MOLPay result = "+data.getStringExtra(MOLPayActivity.MOLPayTransactionResult));
        }
    }
    
    =========================================
    Sample transaction result in JSON string:
    =========================================
    
    {"status_code":"11","amount":"1.01","chksum":"34a9ec11a5b79f31a15176ffbcac76cd","pInstruction":0,"msgType":"C6","paydate":1459240430,"order_id":"3q3rux7dj","err_desc":"","channel":"Credit","app_code":"439187","txn_ID":"6936766"}
    
    Parameter and meaning:
    
    "status_code" - "00" for Success, "11" for Failed, "22" for *Pending. 
    (*Pending status only applicable to cash channels only)
    "amount" - The transaction amount
    "paydate" - The transaction date
    "order_id" - The transaction order id
    "channel" - The transaction channel description
    "txn_ID" - The transaction id generated by MOLPay
    
    * Notes: You may ignore other parameters and values not stated above
    
    =====================================
    * Sample error result in JSON string:
    =====================================
    
    {"Error":"Communication Error"}
    
    Parameter and meaning:
    
    "Communication Error" - Error starting a payment process due to several possible reasons, please contact MOLPay support should the error persists.
    1) Internet not available
    2) API credentials (username, password, merchant id, verify key)
    3) MOLPay server offline.

## Prepare the Payment detail object

    HashMap<String, Object> paymentDetails = new HashMap<>();
    
        // Optional, REQUIRED when use online Sandbox environment and account credentials.
        paymentDetails.put(MOLPayActivity.mp_dev_mode, false);
    
        // Mandatory String. Values obtained from MOLPay.
        paymentDetails.put(MOLPayActivity.mp_username, "username");
        paymentDetails.put(MOLPayActivity.mp_password, "password");
        paymentDetails.put(MOLPayActivity.mp_merchant_ID, "merchantid");
        paymentDetails.put(MOLPayActivity.mp_app_name, "appname");
        paymentDetails.put(MOLPayActivity.mp_verification_key, "vkey123");
    
        // Mandatory String. Payment values.
        paymentDetails.put(MOLPayActivity.mp_amount, "1.10"); // Minimum 1.01
        paymentDetails.put(MOLPayActivity.mp_order_ID, "orderid123");
        paymentDetails.put(MOLPayActivity.mp_currency, "MYR");
        paymentDetails.put(MOLPayActivity.mp_country, "MY");
        
        // Optional, but required payment values. User input will be required when values not passed.
        paymentDetails.put(MOLPayActivity.mp_channel, "multi"); // Use 'multi' for all available channels option. For individual channel seletion, please refer to https://github.com/MOLPay/molpay-mobile-xdk-examples/blob/master/channel_list.tsv.
        paymentDetails.put(MOLPayActivity.mp_bill_description, "billdesc");
        paymentDetails.put(MOLPayActivity.mp_bill_name, "billname");
        paymentDetails.put(MOLPayActivity.mp_bill_email, "email@domain.com");
        paymentDetails.put(MOLPayActivity.mp_bill_mobile, "+1234567");
    
        // Optional, allow channel selection. 
        paymentDetails.put(MOLPayActivity.mp_channel_editing, false);
    
        // Optional, allow billing information editing.
        paymentDetails.put(MOLPayActivity.mp_editing_enabled, false);
    
        // Optional, for Escrow.
        paymentDetails.put(MOLPayActivity.mp_is_escrow, ""); // Put "1" to enable escrow
    
        // Optional, for credit card BIN restrictions and campaigns.
        String binlock[] = {"414170","414171"};
        paymentDetails.put(MOLPayActivity.mp_bin_lock, binlock);
    
        // Optional, for mp_bin_lock alert error.
        paymentDetails.put(MOLPayActivity.mp_bin_lock_err_msg, "Only UOB allowed");
        
        // WARNING! FOR TRANSACTION QUERY USE ONLY, DO NOT USE THIS ON PAYMENT PROCESS.
        // Optional, provide a valid cash channel transaction id here will display a payment instruction screen. Required if mp_request_type is 'Receipt'.
        paymentDetails.put(MOLPayActivity.mp_transaction_id, "");
        // Optional, use 'Receipt' for Cash channels, and 'Status' for transaction status query.
        paymentDetails.put(MOLPayActivity.mp_request_type, "");
    
        // Optional, use this to customize the UI theme for the payment info screen, the original XDK custom.css file can be obtained at https://github.com/MOLPay/molpay-mobile-xdk-examples/blob/master/custom.css.
        paymentDetails.put(MOLPayActivity.mp_custom_css_url, "file:///android_asset/custom.css");
    
        // Optional, set the token id to nominate a preferred token as the default selection, set "new" to allow new card only.
        paymentDetails.put(MOLPayActivity.mp_preferred_token, "");
    
        // Optional, credit card transaction type, set "AUTH" to authorize the transaction.
        paymentDetails.put(MOLPayActivity.mp_tcctype, "");
    
        // Optional, required valid credit card channel, set true to process this transaction through the recurring api, please refer the MOLPay Recurring API pdf. 
        paymentDetails.put(MOLPayActivity.mp_is_recurring, false);
    
        // Optional, show nominated channels.
        String allowedchannels[] = {"credit","credit3"};
        paymentDetails.put(MOLPayActivity.mp_allowed_channels, allowedchannels);
    
        // Optional, simulate offline payment, set boolean value to enable. 
        paymentDetails.put(MOLPayActivity.mp_sandbox_mode, true);
    
        // Optional, required a valid mp_channel value, this will skip the payment info page and go direct to the payment screen.
        paymentDetails.put(MOLPayActivity.mp_express_mode, true);
    
        // Optional, extended email format validation based on W3C standards.
        paymentDetails.put(MOLPayActivity.mp_advanced_email_validation_enabled, true);
    
        // Optional, extended phone format validation based on Google i18n standards.
        paymentDetails.put(MOLPayActivity.mp_advanced_phone_validation_enabled, true);
    
        // Optional, explicitly force disable user input.
        paymentDetails.put(MOLPayActivity.mp_bill_name_edit_disabled, true);
        paymentDetails.put(MOLPayActivity.mp_bill_email_edit_disabled, true);
        paymentDetails.put(MOLPayActivity.mp_bill_mobile_edit_disabled, true);
        paymentDetails.put(MOLPayActivity.mp_bill_description_edit_disabled, true);
    
        // Optional, EN, MS, VI, TH, FIL, MY, KM, ID, ZH.
        paymentDetails.put(MOLPayActivity.mp_language, "EN");
    
        // Optional, Cash channel payment request expiration duration in hour.
        paymentDetails.put(MOLPayActivity.mp_cash_waittime, 48);
        
        // Optional, allow bypass of 3DS on some credit card channels.
        paymentDetails.put(MOLPayActivity.mp_non_3DS, true);
    
        // Optional, disable card list option.
        paymentDetails.put(MOLPayActivity.mp_card_list_disabled, true);
    
        // Optional for channels restriction, this option has less priority than mp_allowed_channels.
        String disabledChannels[] = {"credit"};
        paymentDetails.put(MOLPayActivity.mp_disabled_channels, disabledChannels);


## Start the payment module

    startActivityForResult(intent, MOLPayActivity.MOLPayXDK);

## Simple payment request builder

    Copy the MOLPay.java in the example and drop into your project to easily build a payment request.

    Intent intent = MOLPay.IntentBuilder.from(this)
        // fill up mandatory fields
        .withMandatoryFields()
        .withAppUserName("").withAppPassword("").withAppName("")
        .withMerchantID("").withVerificationKey("")
        .withAmount("1.01").withCurrency("MYR").withCountry("MY")
        .build()

        // fill up optional fields, more methods not shown
        .withOptionalFields()
        .withBillName("").withBillDescription("").withBillEmail("").withBillMobile("")
        .withEditingEnabled().withBillDescriptionEditDisabled().build()
        .withAllowedChannels(new String[]{"credit", "credit3", "GPayPal", "alipay"})
        .withSandboxMode().build()
        .build();

    startActivityForResult(intent, MOLPayActivity.MOLPayXDK);

## Cash channel payment process (How does it work?)

    This is how the cash channels work on XDK:
    
    1) The user initiate a cash payment, upon completed, the XDK will pause at the “Payment instruction” screen, the results would return a pending status.
    
    2) The user can then click on “Close” to exit the MOLPay XDK aka the payment screen.
    
    3) When later in time, the user would arrive at say 7-Eleven to make the payment, the host app then can call the XDK again to display the “Payment Instruction” again, then it has to pass in all the payment details like it will for the standard payment process, only this time, the host app will have to also pass in an extra value in the payment details, it’s the “mp_transaction_id”, the value has to be the same transaction returned in the results from the XDK earlier during the completion of the transaction. If the transaction id provided is accurate, the XDK will instead show the “Payment Instruction" in place of the standard payment screen.
    
    4) After the user done the paying at the 7-Eleven counter, they can close and exit MOLPay XDK by clicking the “Close” button again.

## XDK built-in checksum validator caveats 

    All XDK come with a built-in checksum validator to validate all incoming checksums and return the validation result through the "mp_secured_verified" parameter. However, this mechanism will fail and always return false if merchants are implementing the private secret key (which the latter is highly recommended and prefereable.) If you would choose to implement the private secret key, you may ignore the "mp_secured_verified" and send the checksum back to your server for validation. 

## Private Secret Key checksum validation formula

    chksum = MD5(mp_merchant_ID + results.msgType + results.txn_ID + results.amount + results.status_code + merchant_private_secret_key)

## Support

Submit issue to this repository or email to our support-sa@razer.com

Merchant Technical Support / Customer Care : support-sa@razer.com<br>
Sales/Reseller Enquiry : sales-sa@razer.com<br>
Marketing Campaign : marketing-sa@razer.com<br>
Channel/Partner Enquiry : channel-sa@razer.com<br>
Media Contact : media-sa@razer.com<br>
R&D and Tech-related Suggestion : technical-sa@razer.com<br>
Abuse Reporting : abuse-sa@razer.com
