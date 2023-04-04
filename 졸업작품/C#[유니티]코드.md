## **최신날짜 : 4.4**
### **구현 코드**
- [**TouchCode**](#touchcode--터치보스-체력감소골드-증가각종-텍스트-출력)
- [**UpgradeWaepon**](#upgradewaepon--강화를-누를-시-무기-변경-및-각종-설정)
- [**Changeimg_Boss**](#changeimg_boss--보스-체력-소진-시-다음-보스로-이미지-변경)
- [**changeimg_Waepon**](#changeimg_waepon--강화버튼-누를-시-무기-변경강화-버튼과-연동)
<hr>

- #### **TouchCode : 터치,보스 체력감소,골드 증가,강화 시 돈 감소,각종 텍스트 출력**
    ```C# 
    using System.Collections;
    using System.Collections.Generic;
    using UnityEngine;
    using UnityEngine.UI;
    using UnityEngine.EventSystems;

    public class TouchCode : MonoBehaviour, IPointerDownHandler //터치 구현
    {
    UpgradeWaepon Attack; //UpgradeWaepon 코드의 변수,함수를 참조
    
    public int Health = 10; //초기 보스 체력
    public int Level = 1; //초기 레벨
    GameObject Gold; //게임머니 게임오브젝트 선언
    public int Money; //게임머니 선언
    public int NowDamage; //데미지 선언
    
    public int NowWaepon; //돈 소비를 위한 선언
    
    //무기 공격력과,보스 HP를 표시하기 위한 게임오브젝트 선언
    GameObject ATK;
    GameObject HP;
    

    

    public void OnPointerDown(PointerEventData eventData) //터치 시 변경 사항 넣기
    {
        //강화 버튼을 누를 시 Attack이라는 변수에 UpgradeWaepon 코드 내용을 불러온다.

        //데미지를 실시간으로 출력해준다.
        GameObject obj = GameObject.Find ("UpgradeyelloButton");
		if (obj != null)
			Attack = obj.GetComponent<UpgradeWaepon>();
        NowDamage = Attack.WaeponDamage + 1;
        
        
        

        //보스 레벨 및 체력 설정 조건문

        //보스1을 잡았을 시 실행되는 조건문
        if(Level == 1 && Health <= NowDamage){
            Money += 10; //돈 증가
            Level += 1; //레벨 증가
            Health = 150 + NowDamage; //체력 설정
            
    
        }
        
        if(Level == 2 && Health <= NowDamage){
            Money += 30;
            Level += 1;
            Health = 300 + NowDamage;
        }
        if(Level == 3 && Health <= NowDamage){
            Money += 50;
            Level += 1;
            Health = 450 + + NowDamage;
        }
        if(Level == 4 && Health <= NowDamage){
            Money += 80;
            Level += 1;
            Health = 500 + NowDamage;
        }
        if(Level == 5 && Health <= NowDamage){
            Money += 100;
            Level += 1;
            Health = 550 + NowDamage;
        }

        //터치 시 UpgradeWaepon코드에 있는 무기 데미지를 가져와 체력을 닳게 한다.
        Health -= Attack.WaeponDamage + 1; 
        
        //무기데미지에 맞게 돈을 String형태로 출력하게 한다.
        Money += Attack.WaeponDamage + 1;
        
        

        
        
        
    }
      
    // Start is called before the first frame update
    void Start()
    {
        
        

        
    }

    // Update is called once per frame
    void Update()
    {
        //데미지를 실시간으로 출력해준다.
        GameObject obj = GameObject.Find ("UpgradeyelloButton");
		if (obj != null)
			Attack = obj.GetComponent<UpgradeWaepon>();
        NowDamage = Attack.WaeponDamage + 1;
        
        //UpgradeWapeon HWM(현재 무기 정보)값을 가져온다.
        NowWaepon = Attack.HWM;
        
        //무기 강화 시 감소되는 돈 조건문
        if(NowWaepon== 2 && Attack.WLevel == 2){
            Money -= 30;
            Attack.WLevel = 0;
        }

        if(NowWaepon== 3 && Attack.WLevel == 3){
            Money -= 40;
            Attack.WLevel = 0;
        }
        if(NowWaepon== 4 && Attack.WLevel == 4){
            Money -= 50;
            Attack.WLevel = 0;
        }
        if(NowWaepon== 5 && Attack.WLevel == 5){
            Money -= 60;
            Attack.WLevel = 0;
        }
        if(NowWaepon== 6 && Attack.WLevel == 6){
            Money -= 70;
            Attack.WLevel = 0;
        }
        
        this.Gold = GameObject.Find("Money_number"); //소유금액 실시간 출력
        this.Gold.GetComponent<Text>().text = Money.ToString();

        this.ATK = GameObject.Find("Atk");//공격력 실시간 출력
        this.ATK.GetComponent<Text>().text = "ATK:" + NowDamage.ToString();

        this.HP = GameObject.Find("Health");//보스 HP 실시간 출력
        this.HP.GetComponent<Text>().text = "BOSS_HP:" + Health.ToString();
        
    }
    }
    ``` 

- #### **UpgradeWaepon : 강화를 누를 시 무기 변경 및 각종 설정**
    ```C#
    using System.Collections;
    using System.Collections.Generic;
    using UnityEngine;
    using UnityEngine.UI;
    using UnityEngine.EventSystems;

    public class UpgradeWaepon : MonoBehaviour,IPointerDownHandler
    {
    

    public int UpgradeValue; //랜덤값(확률을 넣기 위한 변수)
    public int WaeponDamage = 1;//초기 무기 데미지
    public int WaeponLevel = 1;//초기 무기 레벨
    public int Upgrade;//강화 확률 
    GameObject Index;//강화 확률을 넣기 위한 게임오브젝트 변수

    TouchCode UpgradeMoney; //TouchCode 변수 가져오기
    
    //강화 시 골드 소비를 위한 변수 선언
    public int HWM;
    public int WLevel;
    
    //소모 강화 비용을 위한 변수 선언
    public int HowMoney; 

    //레벨_텍스트를 나타나기 위한 변수 선언
    GameObject TextLevel;
    //강화필요골드_텍스트를 나타나기 위한 변수 선언
    GameObject TextGold;
    //강화여부_텍스트를 나타나기 위한 변수 선언
    GameObject TextHow;
    public string HowM;
   

   

    public void OnPointerDown(PointerEventData eventData)
    {
        GameObject obj = GameObject.Find ("Monster");
		if (obj != null)
			UpgradeMoney = obj.GetComponent<TouchCode>();
        
        UpgradeValue = Random.Range(0,101); //랜덤값을 넣어줘서 확률로 사용한다.
        Debug.Log(UpgradeValue);

        
        //가지고 있는 돈이 업그레이드 해야 할 돈보다 적으면 강화가 진행되지 않는다.
        //첫 번째 강화->30원보다(UpgradeMoney.Money < 30) 적으면 초기값인 WaeponLevel == 1문이 실행 됨으로서 강화가 진행되지 않는다.
        //그 이후 강화 -> 소유금액이 강화 금액보다 부족하면 (UpgradeMoney.Money<HowMoney)강화 진행이 되지 않는다.
        if(UpgradeMoney.Money<HowMoney || UpgradeMoney.Money < 30){
            
            //LV1->2
            if(WaeponLevel == 1){
                Upgrade = 100; //초기 강화확률 나타내기
                HowMoney = 30; //강화 금액
                HowM = "골드가 부족합니다.";
            }
            //LV2->3
            if(WaeponLevel == 2){
                HowMoney = 40; //강화 금액
                HowM = "골드가 부족합니다.";
            }
            //LV3->4
            if(WaeponLevel == 3){
                HowMoney = 50;
                HowM = "골드가 부족합니다.";
            }
            //LV4->5
            if(WaeponLevel == 4){
                HowMoney = 60;
                HowM = "골드가 부족합니다.";
            }
            //LV5->6
            if(WaeponLevel == 5){
                HowMoney = 70;
                HowM = "골드가 부족합니다.";
            }
            //LV6->7(미구현)
            if(WaeponLevel == 6){
                HowMoney = 80;
                HowM = "골드가 부족합니다.";
            }
            

        }
        
        //무기레벨에 맞는 강화와 무기 레벨 조건문(소유금액이 강화 조건 금액보다 많을 시 강화)
        else if(WaeponLevel == 1 && UpgradeMoney.Money >= 30)
        {
            
            if(UpgradeValue > 0){
                WaeponDamage += 4;
                Debug.Log("2단계 무기 강화 성공 현재 데미지:5");
                Upgrade = 95;//강화활률
                WLevel = 2;//현재 무기 레벨
                HWM = 2;//[TouchCode]강화 조건문을 위한 레벨
                WaeponLevel += 1;
                HowM = "강화성공!";
            }
            

        }
        
        
        //LV2->3
        else if(WaeponLevel == 2 && UpgradeMoney.Money >= 40)
        {
            
            if(UpgradeValue > 5){
                WaeponDamage = 9;
                Debug.Log("3단계 무기 강화 성공! 현재 데미지:10");
                Upgrade = 90;
                WLevel = 3;
                HWM = 3;
                WaeponLevel += 1;
                HowM = "강화성공!";
                
            }
            else{
                Debug.Log("3단계 무기 강화 실패! 현재 데미지:5");
                WLevel = 3;
                HWM = 3;
                HowM = "강화실패!";
            }
        }
        //LV3->4
        else if(WaeponLevel == 3 && UpgradeMoney.Money >= 50)
        {
            
            if(UpgradeValue > 10){
                WaeponDamage = 14;
                Debug.Log("4단계 무기 강화 성공! 현재 데미지:15");
                Upgrade = 85;
                
                WLevel = 4;
                HWM = 4;
                WaeponLevel += 1;
                HowM = "강화성공!";
                
            }
            else{
                Debug.Log("4단계 무기 강화 실패! 현재 데미지:10");
                WLevel = 4;
                HWM = 4;
                HowM = "강화실패!";
            }
        }
        //LV4->5
        else if(WaeponLevel == 4 && UpgradeMoney.Money >= 60)
        {
            
            if(UpgradeValue > 15){
                WaeponDamage = 19;
                Debug.Log("5단계 무기 강화 성공! 현재 데미지:20");
                Upgrade = 80;
                
                WLevel = 5;
                HWM = 5;
                WaeponLevel += 1;
                HowM = "강화성공!";
                
                
            }
            else{
                Debug.Log("5단계 무기 강화 실패! 현재 데미지:15");
                WLevel = 5;
                HWM = 5;
                HowM = "강화실패!";
            }
 
        }
        //LV5->6
        else if(WaeponLevel == 5 && UpgradeMoney.Money >= 70)
        {
            
            if(UpgradeValue > 20){
                WaeponDamage = 24;
                Debug.Log("6단계 무기 강화 성공! 현재 데미지:25");
                Upgrade = 75;
                
                WLevel = 6;
                HWM = 6;
                
                WaeponLevel += 1;
                HowM = "강화성공!";
                
            }
            else{
                Debug.Log("6단계 무기 강화 실패! 현재 데미지:20");
                WLevel = 6;
                HWM = 6;
                HowM = "강화실패!";
            }
        }
        
        
        
        

        //오브젝트인 강화 성공 확률(Upgrade_number)를 실시간으로 변화 시켜준다.
        this.Index = GameObject.Find("Upgrade_number");
        this.Index.GetComponent<Text>().text = Upgrade.ToString() + "%";
        //무기레벨을 실시간으로 출력
        this.TextLevel = GameObject.Find("Level");
        this.TextLevel.GetComponent<Text>().text = "Waepons_LV:" + WaeponLevel.ToString();
        //강화금액을 실시간으로 출력
        this.TextGold = GameObject.Find("Money");
        this.TextGold.GetComponent<Text>().text = "Upgrade_Money:" + HowMoney.ToString();
        //강화여부를 실시간으로 출력
        this.TextHow = GameObject.Find("How");
        this.TextHow.GetComponent<Text>().text = HowM;  
         
        
       
        //무기데미지에 맞게 돈을 String형태로 출력하게 한다.
    }
    
    // Start is called before the first frame update
    
        
        
        

    
    void Start()
    {
        
        
    }

    // Update is called once per frame
    void Update()
    {
       
        

        
    }
    }

    ```

- #### **Changeimg_Boss : 보스 체력 소진 시 다음 보스로 이미지 변경** 
    ```C#
    using System.Collections;
    using System.Collections.Generic;
    using UnityEngine;
    using UnityEngine.UI;

    public class Changeimg_Boss : TouchCode 
    {
    private Image image; //Image 선언

    [SerializeField]
    private Sprite[] sprites; //Image Sprite 형식 선언
    
    // Start is called before the first frame update
    void Start()
    {
        
        //초기 이미지를 선언/불러온다
        image = GetComponent<Image>();
        image.sprite = sprites[0];
        
    }
    //보스레벨에 맞게 이미지를 변경하는 함수
    void Boss2(){
    
        image.sprite = sprites[1];
    }
    void Boss3(){
    
        image.sprite = sprites[2];
    }

    void Boss4(){
    
        image.sprite = sprites[3];
    }

    void Boss5(){
    
        image.sprite = sprites[4];
    }
    void Boss6(){
    
        image.sprite = sprites[5];
    }

    // Update is called once per frame

    //보스 레벨에 맞게 이미지를 변경하는 조건문/함수선언
    void Update()
    {
        if(Level == 2){
            Boss2();
        }
        if(Level == 3){
            Boss3();
        }
        if(Level == 4){
            Boss4();
        }
        if(Level == 5){
            Boss5();
        }
        if(Level == 6){
            Boss6();
        }
        
    }
    }

    ```
- #### **Changeimg_Waepon : 강화버튼 누를 시 무기 변경,강화 버튼과 연동**
    ```C#
    using System.Collections;
    using System.Collections.Generic;
    using UnityEngine;
    using UnityEngine.UI;

    public class Changeimg_Waepon : UpgradeWaepon
    {
    private Image image; //Image 선언
    UpgradeWaepon Waepons; //UpgradeWaepon 값을 넣기 위한 변수 선언

    [SerializeField]
    private Sprite[] sprites; //Image Sprite 형식 선언

    
    public int Waponsindex = 0; //조건문을 위한 변수 선언
    
    // Start is called before the first frame update
    void Start()
    {

        //초기에 값을 넣기 위해 obj라는 변수 선언에 UpgradeyelloButton에 있는 UpgradeWaepon 변수를 불러온다.  
        GameObject obj = GameObject.Find ("UpgradeyelloButton");
		if (obj != null){
			Waepons = obj.GetComponent<UpgradeWaepon>();
        }
        
        
        //초기 이미지를 선언/불러온다
        image = GetComponent<Image>();
        image.sprite = sprites[0];
    }
    //무기레벨에 맞게 이미지를 변경하는 함수
    void WLevel2(){
    
        image.sprite = sprites[1];
    }
    void WLevel3(){
    
        image.sprite = sprites[2];
    }

    void WLevel4(){
    
        image.sprite = sprites[3];
    }

    void WLevel5(){
    
        image.sprite = sprites[4];
    }
    void WLevel6(){
    
        image.sprite = sprites[5];
    }

    // Update is called once per frame

    //보스 레벨에 맞게 이미지를 변경하는 조건문/함수선언
    void Update()
    {
        //지속적으로 변화 시키기 위해 Updatde 문에다가 UpgradeWaepon 변수 값을 넣어준다.
        GameObject obj = GameObject.Find ("UpgradeyelloButton");
		if (obj != null){
			Waepons = obj.GetComponent<UpgradeWaepon>();
        }
        //변수 안에다가 레벨값을 넣어준다.
        Waponsindex = Waepons.WaeponLevel;

        //레벨값에 맞는 이미지 변화를 위한 조건문
        if(Waponsindex == 2){
            WLevel2();
        }
        if(Waponsindex == 3){
            WLevel3();
        }
        if(Waponsindex == 4){
            WLevel4();
        }
        if(Waponsindex == 5){
            WLevel5();
        }
        if(Waponsindex == 6){
            WLevel6();
        }
        
    }
    }
    ```      

