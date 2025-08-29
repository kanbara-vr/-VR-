# -VR-
UIテキスト+カウント・AI挙動・AI増殖

【難易度選択】
・かんたん
  public GameObject TargetObject;
    public GameObject TargetObject1;
    public GameObject TargetObject2;
    public New5 aiScript; // AI本体のスクリプト参照
    public UdonBehaviour targetScript;  // 停止したいスクリプトをここで指定
    void Start()
    {
        targetScript.enabled = false;
    }
    public override void Interact()
    {

        TargetObject.SetActive(false);


        TargetObject1.SetActive(true);

        TargetObject2.SetActive(false);
        aiScript.switchTriggered = false;
        aiScript.switchTriggered1 = false;
        targetScript.enabled = false;
    }

・ふつう
 public GameObject TargetObject;
    public GameObject TargetObject1;
    public GameObject TargetObject2;
    public New5 aiScript; // AI本体のスクリプト参照
    public UdonBehaviour targetScript;  // 停止したいスクリプトをここで指定
    void Start()
    {
        targetScript.enabled = false;
    }
    public override void Interact()
    {

        TargetObject.SetActive(false);


        TargetObject1.SetActive(false);

        TargetObject2.SetActive(true);
        aiScript.switchTriggered = false;
        aiScript.switchTriggered1 = true;
        targetScript.enabled = false;
    }

・むずかしい
  public GameObject TargetObject;
    public GameObject TargetObject1;
    public GameObject TargetObject2;
    public New5 aiScript; // AI本体のスクリプト参照
    public seisei aiScript1; // AI本体のスクリプト参照
    public UdonBehaviour targetScript;  // 停止したいスクリプトをここで指定
    void Start()
    {
        targetScript.enabled = false;
    }
    public override void Interact()
    {
       
            TargetObject.SetActive(true);
        aiScript.switchTriggered = true;
        aiScript.switchTriggered1 = true;
        aiScript1.switchTriggered2 = true;

        TargetObject1.SetActive(false);
       
            TargetObject2.SetActive(false);
       
       
        targetScript.enabled = true;
    }

【AI難易度】
 public NavMeshAgent agent; // NavMeshAgentコンポーネント
   

    private VRCPlayerApi localPlayer;  // ローカルプレイヤー
    public Animator aiAnimator;  // アニメーションを持つAIオブジェクトのAnimator
    public string animationTriggerName = "moveSpeed ";  // トリガー名（Animatorのパラメータ名）
    public string animationTriggerName1 = "attackRange ";
  
    public string animationTriggerName4 = "kt ";
    public string animationTriggerName5 = "tyakuti ";
　public Transform target;  // 初期ターゲット
    public float rotationSpeed = 200f; // 振り向くスピード
  public float triggerDistancemax = 15.0f;  // プレイヤーとAIの距離の閾値
    public float triggerDistancemin = 6.0f;  // プレイヤーとAIの距離の閾値
    public GameObject particleObject; // パーティクルオブジェクト
　private float timer = 0f;
    private bool isStopped = false;
　private float followDistance = 5.0f; // パーティクルとの距離の閾値

    public Transform aiTransform;   // AIのTransform
    public float Angle = 1f; // 攻撃アニメーションをトリガーする角度の閾値

    public UdonBehaviour targetScript1;  // 停止したいスクリプトをここで指定
   
  
    private Vector3 startPosition;
    private Quaternion startRotation;
    public bool switchTriggered = false; // スイッチ押したらtrue
    public bool switchTriggered1 = false; // スイッチ押したらtrue


    void Start()
    {

        if (agent == null)
        {
            agent = GetComponent<NavMeshAgent>();
            agent.updateRotation = false; // NavMeshAgentの回転を無効化
        }
       
        // ローカルプレイヤーを取得
        localPlayer = Networking.LocalPlayer;
     

       
        targetScript1.enabled = false;
        aiAnimator = GetComponent<Animator>();
       
      

    }

    void Update()
    {

        if (localPlayer == null || aiAnimator == null || target == null) return;
        // アニメーションが再生中かどうかをチェック
        // アニメーションの状態を確認

        aiAnimator.SetBool(animationTriggerName, true);

        // ローカルプレイヤーの位置を目標地点として設定する
        agent.SetDestination(localPlayer.GetPosition());
        float distance = Vector3.Distance(localPlayer.GetPosition(), transform.position);

        aiAnimator.SetTrigger(animationTriggerName);
        targetScript1.enabled = false;
       
        // 指定の距離以内に入ったらアニメーションを再生
        if (distance <= triggerDistancemax)
        {

          
            aiAnimator.SetBool(animationTriggerName, false);
            aiAnimator.SetBool(animationTriggerName1, true);
            agent.speed = 1f;

            targetScript1.enabled = false;
        

            // AIの前方向ベクトル
            Vector3 aiForward = aiTransform.forward;

            // Y軸を無視して水平面のみで計算
            // プレイヤーの位置とのベクトルを取得
            Vector3 directionToPlayer = localPlayer.GetPosition() - transform.position;
            directionToPlayer.y = 0;
            aiForward.y = 0;

            // プレイヤーとの角度を計算（符号付き）
            float angle = Vector3.Angle(transform.forward, directionToPlayer);
            // 左20度以上なら攻撃アニメーション




            if (switchTriggered1)
            {


                if (angle >= 5f)
            {

                aiAnimator.SetBool(animationTriggerName1, false);
                aiAnimator.SetBool(animationTriggerName4, true);
                timer += Time.deltaTime;

                    // 6秒から10秒の間は追従停止
                    if (timer >= 6f && timer <= 10f)
                    {
                        if (!isStopped)
                        {
                            // プレイヤーの位置を取得
                            Vector3 playerPosition = localPlayer.GetPosition();

                            // オブジェクトの位置を取得
                            Vector3 objectPosition = transform.position;

                            // プレイヤーの位置を向かせる方向を計算
                            Vector3 directionlocalPlayer = playerPosition - objectPosition;

                            // オブジェクトの向きをプレイヤーに向ける
                            transform.rotation = Quaternion.LookRotation(directionlocalPlayer);

                            agent.isStopped = true;  // 追従停止
                            isStopped = true;
                            aiAnimator.SetBool("attackRange", false);  // アニメーション制御
                        }

                        agent.speed = 3f;
                        rotationSpeed = 200f;

                    }
                    else
                    {
                        if (isStopped || timer > 10f)
                        {
                            // プレイヤーの位置を取得
                            Vector3 playerPosition = localPlayer.GetPosition();

                            // オブジェクトの位置を取得
                            Vector3 objectPosition = transform.position;

                            // プレイヤーの位置を向かせる方向を計算
                            Vector3 directionlocalPlayer = playerPosition - objectPosition;

                            // オブジェクトの向きをプレイヤーに向ける
                            transform.rotation = Quaternion.LookRotation(directionlocalPlayer);
                            agent.isStopped = false;  // 追従再開
                            isStopped = false;
                            aiAnimator.SetBool("attackRange", true);
                        }

                        // ターゲット追従
                        if (target != null)
                        {
                            agent.SetDestination(target.position);
                        }
                        // 10秒でリセット（ループしたい場合）
                        if (timer > 10f)
                        {
                            timer = 0f;
                        }

                        if (angle >= 1f)
                        {
                            targetScript1.enabled = true;
                            agent.speed = 1f;


                        }

                        else if (angle <= 1f)
                        {
                            aiAnimator.SetBool(animationTriggerName4, false);
                            aiAnimator.SetBool(animationTriggerName1, true);

                            agent.speed = 1f;




                        }
                    }

                    if (switchTriggered)
                    {
                        if (distance <= triggerDistancemin)
                        {
                         
                            if (targetObject2 != null)
                            {
                                targetObject2.SetActive(true); // オブジェクトを表示
                            }
                            if (targetObject1 != null)
                            {
                                targetObject1.SetActive(false); // オブジェクトを表示
                            }
                            aiAnimator.SetBool(animationTriggerName, false);
                            aiAnimator.SetBool(animationTriggerName1, false);
                            aiAnimator.SetBool(animationTriggerName4, false);
                        
                            targetScript1.enabled = false;

                            aiAnimator.SetBool(animationTriggerName2, true);









                            agent.speed = 1f;

                        }
                    }

           【AIの生成】
  public int warpCount = 0;
    private int hitCount = 3;
 public GameObject Enemy1;
    public Transform EnemyPlace1;
    int HP = 3;
    public Text HPtext;
 public Transform target; 
 public NavMeshAgent agent;

  void Start()
    {

        if (ParticleSystem == null)
        {
            ParticleSystem = GetComponent<ParticleSystem>();
        }

        HP = 3;
        destroy = 0;
    }
    private void OnParticleCollision(GameObject other)
    {
        HP -= 1;
        HPtext.text = $"HP;{HP}/3";

        if (HP <= 0)
        {
            if (agent != null && target != null)
            {
                // NavMeshAgentを止めてからワープ
                agent.Warp(target.position);

                warpCount += 1;
                if (warpCount >= 3)
                {



                    Instantiate(Enemy1, EnemyPlace1.position, Quaternion.identity);




                    warpCount = 0;
                }

            }




            HP = 3;

        }
       
    }
}

【AI本体登場】
    public Text destroyText; // UI表示用
    public int destroy = 0;
    public Transform target; // テレポート先のオブジェクト
    public Transform target1; // テレポート先のオブジェクト
    public NavMeshAgent agent;
 public bool switchTriggered2 = false; 
 private int hitCount = 0;
 public GameObject TargetObject6;

void Start()
    {
        destroy = 0;
    }

    private void OnTriggerEnter(Collider other)
    {
        if (target != null)
        {
            destroy += 1;
            destroyText.text = $"destroy; {destroy}";

            if (switchTriggered2)
            {
                hitCount++;
                if (hitCount >=3)
                {
                    TargetObject6.SetActive(true);
                    MoveObject();
                }

            }


        }
    }
  
