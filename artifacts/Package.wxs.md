```xml
<!--Author: Thomas Rutherford. Version: 1.0-->
<Wix xmlns="http://wixtoolset.org/schemas/v4/wxs"
     xmlns:ui="http://wixtoolset.org/schemas/v4/wxs/ui"
     xmlns:util="http://wixtoolset.org/schemas/v4/wxs/util">

	<!--Set application info, note, the Product code should be updated
        upon every release. To generate a new Product Code, open View / Terminal,
        then use the guidgen.exe command.
        The Upgrade code should never change.-->
	<Package Name="States Search"
             Manufacturer="Truth Software"
             Version="!(bind.fileVersion.StatesSearchOutput)"
             UpgradeCode="3E002F25-B5D8-43E4-9D5B-96DA70FA5F97"
             ProductCode="3F6BFE66-EB81-403B-BD31-901E42C53DFD">

		<!--Set the downgrade error message-->
		<MajorUpgrade DowngradeErrorMessage="!(loc.DowngradeError)" />

		<!--Embed all the install files into the MSI-->
		<MediaTemplate EmbedCab="yes" />

		<!--Specify what gets included in the setup project output file-->
		<Feature Id="Main">
			<ComponentGroupRef Id="AppComponents" />
			<ComponentRef Id="AppShortcuts" />
		</Feature>

		<!--Set the icon and descriptionshown in the Windows programs list-->
		<Icon Id="AppIcon.exe" SourceFile="StatesSearch.exe"/>
		<Property Id="ARPPRODUCTICON" Value="AppIcon.exe" />
		<Property Id="AppDescription" Value="Application to search US state data using a
		                                     Boyer Moore search algorithm" />

		<UI>
			<!--Setup the UI allowing the user to selecte the install directory -->
			<ui:WixUI
                Id="WixUI_InstallDir"
                InstallDirectory="INSTALLFOLDER" >
			</ui:WixUI>

			<!--Edit the exit dialog to launch app if checkbox is checked-->
			<Publish Dialog="ExitDialog"
                Control="Finish"
                Event="DoAction"
                Value="LaunchApplication"
                Condition="WIXUI_EXITDIALOGOPTIONALCHECKBOX = 1 and NOT Installed">
			</Publish>
		</UI>

    <!--Set the UI dialog background-->
    <WixVariable Id="WixUIDialogBmp" Value="Resources\WixDialog.png" />

    <!--Set the UI dialog banner-->
    <WixVariable Id="WixUIBannerBmp" Value="Resources\WixDialogBanner.png" />

		<!--Specify the license agreement to show in the installer dialogs-->
		<WixVariable Id="WixUILicenseRtf" Value="Resources\MITLicenseAgreement.rtf" />

		<!--Set options and action for 'launch after install' checkbox -->
		<Property Id="WIXUI_EXITDIALOGOPTIONALCHECKBOX" Value="1" />
		<Property Id="WIXUI_EXITDIALOGOPTIONALCHECKBOXTEXT" Value="Launch States Search App" />
		<Property Id="WixShellExecTarget" Value="[#StatesSearchOutput]" />
		<CustomAction Id="LaunchApplication" BinaryRef="Wix4UtilCA_$(sys.BUILDARCHSHORT)"
		              DllEntry="WixShellExec" Impersonate="yes" />
	</Package>
</Wix>
```