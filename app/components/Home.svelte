<page>
    <actionBar title="QR Scanner" />
    <stackLayout>
        {#if scanMsg}
        <label class="text_result" text="Result:" />
        <textView editable="{false}">
            <formattedString>
                <span text="{scanMsg}" />
            </formattedString>
        </textView>
        {/if}

        {#if scanType == 'URL'}
        <button text="Open URL" on:tap="{onOpenURL(scanMsg)}" />
        {/if}

        <button text="Scan Now" class="btn_scan" on:tap="{onTestClick}" />
    </stackLayout>
</page>

<script lang="ts">  
    import { Utils } from '@nativescript/core';
    import { BarcodeScanner } from "nativescript-barcodescanner";
    let barcodescanner = new BarcodeScanner();
    let scanMsg = '';
    let scanType = '';

    const onTestClick = () => {
        barcodescanner.scan({
            formats: "QR_CODE, EAN_13",
            cancelLabel: "EXIT. Also, try the volume buttons!", // iOS only, default 'Close'
            cancelLabelBackgroundColor: "#333333", // iOS only, default '#000000' (black)
            message: "Use the volume buttons for extra light", // Android only, default is 'Place a barcode inside the viewfinder rectangle to scan it.'
            showFlipCameraButton: false,   // default false
            preferFrontCamera: false,     // default false
            showTorchButton: true,        // default false
            beepOnScan: true,             // Play or Suppress beep on scan (default true)
            fullScreen: true,             // Currently only used on iOS; with iOS 13 modals are no longer shown fullScreen by default, which may be actually preferred. But to use the old fullScreen appearance, set this to 'true'. Default 'false'.
            torchOn: false,               // launch with the flashlight on (default false)
            closeCallback: () => { console.log("Scanner closed")}, // invoked when the scanner was closed (success or abort)
            resultDisplayDuration: 500,   // Android only, default 1500 (ms), set to 0 to disable echoing the scanned text
            orientation: 'orientation',     // Android only, default undefined (sensor-driven orientation), other options: portrait|landscape
            openSettingsIfPermissionWasPreviouslyDenied: true, // On iOS you can send the user to the settings app if access was previously denied
            presentInRootViewController: true // iOS-only; If you're sure you're not presenting the (non embedded) scanner in a modal, or are experiencing issues with fi. the navigationbar, set this to 'true' and see if it works better for your app (default false).
        }).then((result) => {
                scanMsg = result.text;
                scanType = isUrl(scanMsg)? 'URL': 'TEXT';
            }, (errorMessage) => {
                console.log("No scan. " + errorMessage);
            }
        );
    }

    const isUrl = (inputStr: string) => {
        return /^(?:\w+:)?\/\/([^\s\.]+\.\S{2}|localhost[\:?\d]*)\S*$/.test(inputStr); 
    }

    const onOpenURL = (callURL: string) => {
        Utils.openUrl(callURL);
        return true;
    }

</script>

<style>
    .btn_scan {
        background-color: #3A53FF;
        color: #ffffff;
    }

    .text_result {
        padding-left: 45px;
        font-weight: 600;
    }
</style>
