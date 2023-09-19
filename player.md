using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class player : MonoBehaviour
{
    //����������
    private Rigidbody m_RigidBody;

    public float fMaxForce = 500.0f;
    private float m_CurForce = 0.0f;
    // Start is called before the first frame update
    void Start()
    {
        //��ȡģ�庯��
        m_RigidBody = GetComponent<Rigidbody>();
    }

    // Update is called once per frame
    void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            // Jump();
            //����
            //m_CurForce += Time.deltaTime * 100;
        }
        else if (Input.GetMouseButton(0))
        {
            m_CurForce += Time.deltaTime * 100;
            if (m_CurForce > fMaxForce)
            {
                m_CurForce = fMaxForce;
            }
        }
        else if (Input.GetMouseButtonUp(0))
        {
            Jump();
            m_CurForce = 0.0f;
        }

        ShowScale();
    }

    //��������
    private void ShowScale()
    {
        float sc = (fMaxForce - m_CurForce*0.5f) / fMaxForce;
      /*  if (sc < 0.5f)
        {
            sc = 0.5f;
        }  */
        Vector3 scale = transform.localScale;
        scale.y = sc * 0.2f;
        transform.localScale = scale;
    }
    
    private void Jump()
    {
        m_RigidBody.AddForce(Vector3.up*m_CurForce);
        m_RigidBody.AddForce(Vector3.forward * m_CurForce);
    }
}
