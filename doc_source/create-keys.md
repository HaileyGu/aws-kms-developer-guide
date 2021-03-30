# Creating keys<a name="create-keys"></a>

You can create [symmetric and asymmetric customer master keys](symmetric-asymmetric.md) \(CMKs\) in the AWS Management Console or by using the [CreateKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateKey.html) operation\. During this process, you determine the cryptographic configuration of your CMK and the origin of its key material\. You cannot change these properties after the CMK is created\. You also set the key policy for the CMK, which you can change at any time\.

If you are creating a CMK to encrypt data you store or manage in an AWS service, create a symmetric CMK\. [AWS services that are integrated with AWS KMS](https://aws.amazon.com/kms/features/#AWS_Service_Integration) use symmetric CMKs to encrypt your data\. These services do not support encryption with asymmetric CMKs\. For help deciding which type of CMK to create, see [How to choose your CMK configuration](symm-asymm-choose.md)\.

When you create a CMK in the AWS KMS console, you are required to give it an alias \(friendly name\)\. The CreateKey operation does not create an alias for the new CMK\. To create an alias for a new or existing CMK, use the [CreateAlias](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateAlias.html) operation\. For detailed information about aliases in AWS KMS, see [Using aliases](kms-alias.md)\.

**Learn more:**
+ For help creating a CMK with imported key material \([key material origin](concepts.md#key-origin) is External\), see [Create a customer master key with no key material](importing-keys-create-cmk.md)\.
+ For help creating a CMK in a custom key store \([key material origin](concepts.md#key-origin) is Custom Key Store \(CloudHSM\)\), see [Creating CMKs in a Custom Key Store](create-cmk-keystore.md)\.
+ For help determining whether an existing CMK is symmetric or asymmetric, see [Identifying symmetric and asymmetric CMKs](find-symm-asymm.md)\.
+ To use your CMKs programmatically and in command line interface operations, you need a [key ID](concepts.md#key-id-key-id) or [key ARN](concepts.md#key-id-key-ARN)\. For detailed instructions, see [Finding the key ID and ARN](find-cmk-id-arn.md)\.
+ For information about quotas that apply to CMKs, see [Quotas](limits.md)\.

**Topics**
+ [Permissions for creating CMKs](#create-key-permissions)
+ [Creating symmetric CMKs](#create-symmetric-cmk)
+ [Creating asymmetric CMKs](#create-asymmetric-cmk)

## Permissions for creating CMKs<a name="create-key-permissions"></a>

To create a CMK in the console or by using the APIs, you must have the following permission in an IAM policy\. Whenever possible, use [condition keys](policy-conditions.md) to limit the permissions\. For an example of an IAM policy for principals who create keys, see [Allow a user to create CMKs](customer-managed-policies.md#iam-policy-example-create-key)\.

**Note**  
Be cautious when giving principals permission to manage tags and aliases\. Changing a tag or alias can allow or deny permission to the CMK\. For details, see [Using ABAC for AWS KMS](abac.md)\.
+ [kms:CreateKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateKey.html) is required\. 
+ [kms:CreateAlias](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateAlias.html) is required to create a CMK in the console where an alias is required for every new CMK\.
+ [kms:TagResource](https://docs.aws.amazon.com/kms/latest/APIReference/API_TagResource.html) is required to add tags while creating the CMK\.

The [kms:PutKeyPolicy](https://docs.aws.amazon.com/kms/latest/APIReference/API_PutKeyPolicy.html) permission is not required to create the CMK\. The `kms:CreateKey` permission includes permission to set the initial key policy\. But you must add this permission to the key policy while creating the CMK to ensure that you can control access to the CMK\. The alternative is using the [BypassLockoutSafetyCheck](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateKey.html#KMS-CreateKey-request-BypassPolicyLockoutSafetyCheck) parameter, which is not recommended\.

## Creating symmetric CMKs<a name="create-symmetric-cmk"></a>

You can create [symmetric CMKs](symm-asymm-concepts.md#symmetric-cmks) in the AWS Management Console or by using the AWS KMS API\. Symmetric key encryption uses the same key to encrypt and decrypt data\. 

### Creating symmetric CMKs \(console\)<a name="create-keys-console"></a>

You can use the AWS Management Console to create customer master keys \(CMKs\)\.

1. Sign in to the AWS Management Console and open the AWS Key Management Service \(AWS KMS\) console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1. In the navigation pane, choose **Customer managed keys**\.

1. Choose **Create key**\.

1. To create a symmetric CMK, for **Key type** choose **Symmetric**\.

   For information about how to create an asymmetric CMK in the AWS KMS console, see [Creating asymmetric CMKs \(console\)](#create-asymmetric-cmks-console)\.

1. Choose **Next**\.

1. Type an alias for the CMK\. The alias name cannot begin with **aws/**\. The **aws/** prefix is reserved by Amazon Web Services to represent AWS managed CMKs in your account\.
**Note**  
Adding, deleting, or updating an alias can allow or deny permission to the CMK\. For details, see [Using ABAC for AWS KMS](abac.md) and [Using aliases to control access to CMKs](alias-authorization.md)\.

   An alias is a display name that you can use to identify the CMK\. We recommend that you choose an alias that indicates the type of data you plan to protect or the application you plan to use with the CMK\.

   Aliases are required when you create a CMK in the AWS Management Console\. They are optional when you use the [CreateKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateKey.html) operation\.

1. \(Optional\) Type a description for the CMK\.

   Enter a description that explains the type of data you plan to protect or the application you plan to use with the CMK\. Don't use the description format that's used for [AWS managed CMKs](concepts.md#aws-managed-cmk)\. The *Default master key that protects my \.\.\. when no other key is defined* description format is reserved for AWS managed CMKs\.

   You can add a description now or update it any time unless the [key state](key-state.md) is `Pending Deletion`\. To add, change, or delete the description of an existing customer managed CMK, [edit the description](editing-keys.md) in the AWS Management Console or use the [UpdateKeyDescription](https://docs.aws.amazon.com/kms/latest/APIReference/API_UpdateKeyDescription.html) operation\.

1. Choose **Next**\.

1. \(Optional\) Type a tag key and an optional tag value\. To add more than one tag to the CMK, choose **Add tag**\.
**Note**  
Tagging or untagging a CMK can allow or deny permission to the CMK\. For details, see [Using ABAC for AWS KMS](abac.md) and [Using tags to control access to CMKs](tag-authorization.md)\.

   When you add tags to your AWS resources, AWS generates a cost allocation report with usage and costs aggregated by tags\. Tags can also be used to control access to a CMK\. For information about tagging CMKs, see [Tagging keys](tagging-keys.md) and [Using ABAC for AWS KMS](abac.md)\. 

1. Choose **Next**\.

1. Select the IAM users and roles that can administer the CMK\.
**Note**  
IAM policies can give other IAM users and roles permission to manage the CMK\.

1. \(Optional\) To prevent the selected IAM users and roles from deleting this CMK, in the **Key deletion** section at the bottom of the page, clear the **Allow key administrators to delete this key** check box\.

1. Choose **Next**\.

1. Select the IAM users and roles that can use the CMK for [cryptographic operations](concepts.md#cryptographic-operations)\.
**Note**  
The AWS account \(root user\) has full permissions by default\. As a result, any IAM policies can also give users and roles permission use the CMK for cryptographic operations\.

1. \(Optional\) You can allow other AWS accounts to use this CMK for cryptographic operations\. To do so, in the **Other AWS accounts** section at the bottom of the page, choose **Add another AWS account** and enter the AWS account identification number of an external account\. To add multiple external accounts, repeat this step\.
**Note**  
To allow principals in the external accounts to use the CMK, Administrators of the external account must create IAM policies that provide these permissions\. For more information, see [Allowing users in other accounts to use a CMK](key-policy-modifying-external-accounts.md)\.

1. Choose **Next**\.

1. Review the key settings that you chose\. You can still go back and change all settings\.

1. Choose **Finish** to create the CMK\.

### Creating symmetric CMKs \(AWS KMS API\)<a name="create-keys-api"></a>

You can use the [CreateKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateKey.html) operation to create a new symmetric customer master key \(CMK\)\. These examples use the [AWS Command Line Interface \(AWS CLI\)](https://aws.amazon.com/cli/), but you can use any supported programming language\. 

This operation has no required parameters\. However, you might also want to use the `Policy` parameter to specify a key policy\. You can change the key policy \([PutKeyPolicy](https://docs.aws.amazon.com/kms/latest/APIReference/API_PutKeyPolicy.html)\) and add optional elements, such as a [description](https://docs.aws.amazon.com/kms/latest/APIReference/API_DescribeKey.html) and [tags](https://docs.aws.amazon.com/kms/latest/APIReference/API_TagResource.html) at any time\. Also, if you are creating a CMK for [imported key material](importing-keys.md) or a CMK in a [custom key store](custom-key-store-overview.md), the `Origin` parameter is required\. 

The `CreateKey` operation doesn't let you specify an alias, but you can use the [CreateAlias](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateAlias.html) operation to create an alias for your new CMK\.

The following is an example of a call to the `CreateKey` operation with no parameters\. This command uses all of the default values\. It creates a symmetric CMK for encrypting and decrypting with key material generated by AWS KMS\.

```
$ aws kms create-key
{
    "KeyMetadata": {
        "Origin": "AWS_KMS",
        "KeyId": "1234abcd-12ab-34cd-56ef-1234567890ab",
        "Description": "",
        "KeyManager": "CUSTOMER",
        "Enabled": true,
        "CustomerMasterKeySpec": "SYMMETRIC_DEFAULT",
        "KeyUsage": "ENCRYPT_DECRYPT",
        "KeyState": "Enabled",
        "CreationDate": 1502910355.475,
        "Arn": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab",
        "AWSAccountId": "111122223333"
        "EncryptionAlgorithms": [
            "SYMMETRIC_DEFAULT"
        ]
    }
}
```

If you do not specify a key policy for your new CMK, the [default key policy](key-policies.md#key-policy-default) that `CreateKey` applies differs from the default key policy that the console applies when you use it to create a new CMK\. 

For example, this call to the [GetKeyPolicy](https://docs.aws.amazon.com/kms/latest/APIReference/API_GetKeyPolicy.html) operation returns the key policy that `CreateKey` applies\. It gives the AWS account access to the CMK and allows it to create AWS Identity and Access Management \(IAM\) policies for the CMK\. For detailed information about IAM policies and key policies for CMKs, see [Authentication and access control for AWS KMS](control-access.md)

```
$ aws kms get-key-policy --key-id 1234abcd-12ab-34cd-56ef-1234567890ab --policy-name default --output text
{
  "Version" : "2012-10-17",
  "Id" : "key-default-1",
  "Statement" : [ {
    "Sid" : "Enable IAM User Permissions",
    "Effect" : "Allow",
    "Principal" : {
      "AWS" : "arn:aws:iam::111122223333:root"
    },
    "Action" : "kms:*",
    "Resource" : "*"
  } ]
}
```

## Creating asymmetric CMKs<a name="create-asymmetric-cmk"></a>

You can create [asymmetric CMKs](symm-asymm-concepts.md#asymmetric-cmks) in the AWS Management Console or by using the AWS KMS API\. An asymmetric CMK represents a public and private key pair that can be used for encryption or signing\. The private key remains within AWS KMS\. To download the public key for use outside of AWS KMS, see [Downloading public keys](download-public-key.md)\.

When creating a CMK to encrypt data in an AWS service, [create a symmetric CMK](#create-symmetric-cmk)\. AWS services do not support asymmetric CMKs for encryption\. For help deciding whether to create a symmetric or asymmetric CMK, see [How to Choose Your CMK Configuration](symm-asymm-choose.md)\. 

### Creating asymmetric CMKs \(console\)<a name="create-asymmetric-cmks-console"></a>

You can use the AWS Management Console to create asymmetric customer master keys \(CMKs\)\. Each asymmetric CMK represents a public and private key pair\.

1. Sign in to the AWS Management Console and open the AWS Key Management Service \(AWS KMS\) console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1. In the navigation pane, choose **Customer managed keys**\.

1. Choose **Create key**\.

1. To create an asymmetric CMK, in **Key type,** choose **Asymmetric**\.

   For information about how to create an symmetric CMK in the AWS KMS console, see [Creating symmetric CMKs \(console\)](#create-keys-console)\.

1. To create an asymmetric CMK for public key encryption, in **Key usage**, choose **Encrypt and decrypt**\. Or, to create an asymmetric CMK for signing messages and verifying signatures, in **Key usage**, choose **Sign and verify**\.

   For help choosing a key usage value, see [Selecting the key usage](symm-asymm-choose.md#symm-asymm-choose-key-usage)\.

1. Select a specification \(**Key spec**\) for your asymmetric CMK\. 

   Often the key spec that you select is determined by regulatory, security, or business requirements\. It might also be influenced by the size of messages that you need to encrypt or sign\. In general, longer encryption keys are more resistant to brute\-force attacks\.

   For help choosing a key spec, see [Selecting the key spec](symm-asymm-choose.md#symm-asymm-choose-key-spec)\.

1. Choose **Next**\.

1. Type an [alias](kms-alias.md) for the CMK\. The alias name cannot begin with **aws/**\. The **aws/** prefix is reserved by Amazon Web Services to represent AWS managed CMKs in your account\.

   An *alias* is a friendly name that you can use to identify the CMK in the console and in some AWS KMS APIs\. We recommend that you choose an alias that indicates the type of data you plan to protect or the application you plan to use with the CMK\. 

   Aliases are required when you create a CMK in the AWS Management Console\. You cannot specify an alias when you use the [CreateKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateKey.html) operation, but you can use the console or the [CreateAlias](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateAlias.html) operation to create an alias for an existing CMK\. For details, see [Using aliases](kms-alias.md)\.

1. \(Optional\) Type a description for the CMK\.

   Enter a description that explains the type of data you plan to protect or the application you plan to use with the CMK\. Don't use the description format that's used for [AWS managed CMKs](concepts.md#aws-managed-cmk)\. The *Default master key that protects my \.\.\. when no other key is defined* description format is reserved for AWS managed CMKs\.

   You can add a description now or update it any time unless the [key state](key-state.md) is `Pending Deletion`\. To add, change, or delete the description of an existing customer managed CMK, [edit the description](editing-keys.md) in the AWS Management Console or use the [UpdateKeyDescription](https://docs.aws.amazon.com/kms/latest/APIReference/API_UpdateKeyDescription.html) operation\.

1. \(Optional\) Type a tag key and an optional tag value\. To add more than one tag to the CMK, choose **Add tag**\.

   When you add tags to your AWS resources, AWS generates a cost allocation report with usage and costs aggregated by tags\. Tags can also be used to control access to a CMK\. For information about tagging CMKs, see [Tagging keys](tagging-keys.md) and [Using ABAC for AWS KMS](abac.md)\. 

1. Choose **Next**\.

1. Select the IAM users and roles that can administer the CMK\.
**Note**  
IAM policies can give other IAM users and roles permission to manage the CMK\.

1. \(Optional\) To prevent the selected IAM users and roles from deleting this CMK, in the **Key deletion** section at the bottom of the page, clear the **Allow key administrators to delete this key** check box\.

1. Choose **Next**\.

1. Select the IAM users and roles that can use the CMK for [cryptographic operations](concepts.md#cryptographic-operations)\.
**Note**  
The AWS account \(root user\) has full permissions by default\. As a result, any IAM policies can also give users and roles permission use the CMK for cryptographic operations\.

1. \(Optional\) You can allow other AWS accounts to use this CMK for cryptographic operations\. To do so, in the **Other AWS accounts** section at the bottom of the page, choose **Add another AWS account** and enter the AWS account identification number of an external account\. To add multiple external accounts, repeat this step\.
**Note**  
To allow principals in the external accounts to use the CMK, Administrators of the external account must create IAM policies that provide these permissions\. For more information, see [Allowing users in other accounts to use a CMK](key-policy-modifying-external-accounts.md)\.

1. Choose **Next**\.

1. Review the key settings that you chose\. You can still go back and change all settings\.

1. Choose **Finish** to create the CMK\.

### Creating asymmetric CMKs \(AWS KMS API\)<a name="create-keys-api"></a>

You can use the [CreateKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateKey.html) operation to create an asymmetric customer master key \(CMK\)\. These examples use the [AWS Command Line Interface \(AWS CLI\)](https://aws.amazon.com/cli/), but you can use any supported programming language\. 

When you create an asymmetric CMK, you must specify the `CustomerMasterKeySpec` parameter, which determines the type of keys you create\. Also, you must specify a `KeyUsage` value of ENCRYPT\_DECRYPT or SIGN\_VERIFY\. You cannot change these properties after the CMK is created\.

The `CreateKey` operation doesn't let you specify an alias, but you can use the [CreateAlias](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateAlias.html) operation to create an alias for your new CMK\.

The following example uses the `CreateKey` operation to create an asymmetric CMK of 4096\-bit RSA keys designed for public key encryption\.

```
$ aws kms create-key --customer-master-key-spec RSA_4096 --key-usage ENCRYPT_DECRYPT
{
    "KeyMetadata": {
        "KeyState": "Enabled",
        "KeyId": "1234abcd-12ab-34cd-56ef-1234567890ab",
        "CustomerMasterKeySpec": "RSA_4096",
        "KeyManager": "CUSTOMER",
        "Description": "",
        "KeyUsage": "ENCRYPT_DECRYPT",
        "Arn": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab",
        "CreationDate": 1569973196.214,
        "EncryptionAlgorithms": [
            "RSAES_OAEP_SHA_1",
            "RSAES_OAEP_SHA_256"
        ],
        "AWSAccountId": "111122223333",
        "Origin": "AWS_KMS",
        "Enabled": true
    }
}
```

The following example command creates an asymmetric CMK that represents a pair of ECDSA keys used for signing and verification\. You cannot create an elliptic curve key pair for encryption and decryption\.

```
$ aws kms create-key --customer-master-key-spec ECC_NIST_P521 --key-usage SIGN_VERIFY
{
    "KeyMetadata": {
        "KeyState": "Enabled",
        "KeyId": "0987dcba-09fe-87dc-65ba-ab0987654321",
        "CreationDate": 1570824817.837,
        "Origin": "AWS_KMS",
        "SigningAlgorithms": [
            "ECDSA_SHA_512"
        ],
        "Arn": "arn:aws:kms:us-west-2:111122223333:key/0987dcba-09fe-87dc-65ba-ab0987654321",
        "AWSAccountId": "111122223333",
        "CustomerMasterKeySpec": "ECC_NIST_P521",
        "KeyManager": "CUSTOMER",
        "Description": "",
        "Enabled": true,
        "KeyUsage": "SIGN_VERIFY"
    }
}
```