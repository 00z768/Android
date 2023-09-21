using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class player : MonoBehaviour
{
    //定义刚体组件
    private Rigidbody m_RigidBody;

    public float fMaxForce = 500.0f;
    private float m_CurForce = 0.0f;

    public GameObject Box=null ;
    public float fMinDistance = 1.2f;
    public float fMaxDistance = 3.0f;
    public float fMinHeight = 0.3f;
    public float fMaxHeight = 2.0f;

    private Vector3 m_Direction = Vector3.forward;
    private float m_Distance = 0.0f;
    private float m_Height = 0.0f;

    private GameObject m_CurCube = null;
    private GameObject m_NextCube = null;
   // private int m_Score = 0;

    private Vector3 m_CameraOffest = Vector3.zero;
    private GameObject m_Plane = null;

    private Animator m_Animator = null;

    private UIManager m_UI = null;
    

    private AudioSource m_AudioPlay = null;
    public AudioClip PressAudio = null;
    public AudioClip JumpAudio = null;
    public AudioClip DownAudio = null;
    // Start is called before the first frame update
    void Start()
    {
        //获取模板函数
        m_RigidBody = GetComponent<Rigidbody>();
        m_Animator = GetComponent<Animator>();
        m_UI = GetComponent<UIManager>();
        m_AudioPlay = GetComponent<AudioSource>();
        m_Plane = GameObject.FindGameObjectWithTag("Plane");
        m_NextCube = GenerateBox();
    }

    // Update is called once per frame
    void Update()
    {
        
        GameObject obj = GetHitObject();
        if (obj != null)
        {
            if (obj.tag == "Cube")
            {
                if (m_CurCube == null)
                {
                    PlayAudio(DownAudio);
                    m_CurCube = obj;//游戏刚刚开始
                    m_CameraOffest = Camera.main.transform.position - m_CurCube.transform.position;
                }
                else  if (m_NextCube == obj)
                {
                    PlayAudio(DownAudio);                               
                    m_UI.SetPowerShow(true);
                    Destroy(m_CurCube);
                    m_CurCube = m_NextCube;
                    AddScore();
                    m_NextCube = GenerateBox();
                    m_RigidBody.Sleep();
                    m_RigidBody.WakeUp();
                    m_Animator.SetBool("Forward", false);
                    m_Animator.SetBool("Left", false);

                }
                ProcessInput();
                ShowScale();

                MoveCameraAndPlane();
            }
            else  if(obj.tag=="Plane")
            {
                m_UI.SetGameOver(true);
            }
        }
        
    }

    private void AddScore()
    {
        Vector3 offest = m_CurCube.transform.position - transform.position;

        offest.y = 0;

        if(offest.sqrMagnitude < 0.2f )
        {
            m_UI.AddScore(2);
        }
        else
            m_UI.AddScore(1);
    }

    private void ProcessInput()
    {
        if (Input.GetMouseButtonDown(0))
        {
            PlayAudio(PressAudio);
        }
        else if (Input.GetMouseButton(0))
        {
            m_CurForce += Time.deltaTime * fMaxForce/2.0f;
            if (m_CurForce > fMaxForce)
            {
                m_CurForce = fMaxForce;
            }
        }
        else if (Input.GetMouseButtonUp(0))
        {
            Jump();
            m_CurForce = 0.0f;
            m_UI.SetPowerShow(false);
        }
        m_UI.ShowPower(m_CurForce, fMaxForce);
    }

    private void MoveCameraAndPlane()
    {
        Camera.main.transform.position = Vector3.Lerp(Camera.main.transform.position,
            m_CurCube.transform.position+m_CameraOffest,Time.deltaTime*2);


        Vector3 pos = m_CurCube.transform.position;
        pos.y = 0;
        m_Plane.transform.position = pos;
    }

    //蓄力表现
    private void ShowScale()
    {
        float sc = (fMaxForce - m_CurForce * 0.5f) / fMaxForce;
        Vector3 scale = transform.localScale;
        scale.y = sc * 0.2f;
        transform.localScale = scale;
    }

    private void Jump()
    {
        PlayAudio(JumpAudio);
        m_RigidBody.AddForce(Vector3.up * m_CurForce);
        Vector3 dir = m_NextCube.transform.position - transform.position;
        dir.y = 0;
        m_RigidBody.AddForce(dir.normalized * m_CurForce);
        if (m_Direction == Vector3.forward)
            m_Animator.SetBool("Forward", true);
        else
            m_Animator.SetBool("Left", true);

        
    }

    private GameObject GenerateBox()
    {
        GameObject obj = GameObject.Instantiate(Box);

        m_Distance = Random.Range(fMinDistance, fMaxDistance);
        m_Height = Random.Range(fMinHeight, fMaxHeight);
        m_Direction = Random.Range(0, 2) == 1 ? Vector3.forward : Vector3.left;

        Vector3 pos = Vector3.zero;
        if (m_CurCube == null)
            pos = m_Direction * m_Distance + transform.position;
        else
            pos = m_Direction * m_Distance + m_CurCube.transform.position;

        pos.y = 2.0f;
        obj.transform.position = pos;

        obj.transform.localScale = new Vector3(1, m_Height, 1);

        obj.GetComponent<MeshRenderer>().material.color = new Color(Random.Range(0.0f, 1.0f), Random.Range(0.0f, 1.0f), Random.Range(0.0f, 1.0f));

        return obj;
    }


    private GameObject GetHitObject()
    {
        RaycastHit hit;
        Vector3[] vOffests = { Vector3.zero,Vector3.forward, Vector3.back, Vector3.left, Vector3.right };
        foreach (Vector3 vof in vOffests)
        {
            if (Physics.Raycast(transform.position + vof * 0.1f, Vector3.down, out hit, 0.3f))
            {
                    Debug.Log(hit.collider.tag);
                    return hit.collider.gameObject;
            }
        }
        

        return null;
    }

    private void OnDrawGizmos()
    {
        Gizmos.color = Color.red;
        Vector3[] vOffests = { Vector3.zero, Vector3.forward, Vector3.back, Vector3.left, Vector3.right };
        foreach (Vector3 vof in vOffests)
        {
            Gizmos.DrawLine(transform.position + vof * 0.1f, transform.position + vof * 0.1f+Vector3.down*0.3f);
        }
    }

    private void PlayAudio(AudioClip clp)
    {
        m_AudioPlay.Stop();
        m_AudioPlay.clip = clp;
        m_AudioPlay.Play();
    }
}
