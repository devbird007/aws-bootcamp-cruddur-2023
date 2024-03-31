# Homework Challenges
  - Destroy your root account credentials, Set MFA, IAM role
  - Use EventBridge to hookup Health Dashboard to SNS and send notification when there is a service health issue.
  - Review all the questions of each pillars in the Well Architected Tool (No specialized lens)
  - Create an architectural diagram (to the best of your ability) the CI/CD logical pipeline in Lucid Charts
  - Research the technical and service limits of specific services and how they could impact the technical path for technical flexibility. 
  - Open a support ticket and request a service limit

## Use EventBridge to hookup Health Dashboard to SNS to send a notification when there is a service health issue
### Procedure

1. Open Amazon SNS
   - #### Creating a Topic:  
       - Under **Create Topic**, enter a name  
       - Under **Type**, select **Standard** and not **FIFO** [default]
       - Click **Create Topic** at the bottom
         
   - #### Creating a Subscription:  
       - Click **Create Subscription** on the resulting page to begin
       - Under **Protocol**, select **Email**
       - Under **Endpoint**, enter your email
       - Click **Create Subscription** at the bottom to end
       - Open your email to confirm your subscrition

2. Open Amazon EventBridge
   - Create EventBridge Rule
   - Under **_Rule detail_**:
        - Under **Name**, enter the rule's name
        - Select **Event bus** [default]
        - Toggle on **Enable the rune on the selected event bus** [default]
        - For **Rule type** select **Rule with an event pattern** [default]
  
    - Under **_Build event pattern_**:
        -  For **Event source**, select **AWS events or EventBridge partner events** [default]
        -  For **Creation method**, select **Use pattern form** selected [default]
        -  For **Event Pattern**:
             - For **Event source** select **AWS services** [default]
             - For **AWS Service**, select **Health**
             - Create your own with **Custom pattern JSON editor**
    
     - Under **_Select target(s)_**:
         - For **Target 1**:
             - Under **Target types** choose **AWS service** [default]
             - Under **Select a Target**, choose **SNS Topic**
             - Under **Topic**, choose your SNS you created above 

      - Configure Tags (optional)
      - Review and Create
          - Click **Create Rule** at the bottom
		
