# lab-03-controllers-deployments-replicasets

# =========================================================
# פקודות Kubernetes - מעבדה 3: בקרים, Deployments ו-ReplicaSets
# =========================================================

# --- 1. יצירת ה-Deployment והפעלה ראשונית ---

# החלת הגדרות ה-Deployment על האשכול (Cluster)
kubectl apply -f nginx-deployment.yaml

# צפייה במצב ה-Deployment (כמה Pod-ים מוכנים מתוך כמה שנדרשו)
kubectl get deployments

# צפייה ב-ReplicaSets שנוצרו על ידי ה-Deployment
kubectl get replicasets

# צפייה ב-Pod-ים שנוצרו בפועל
kubectl get pods

# --- 2. בדיקת היררכיית בעלות (Ownership) ---

# אימות שה-ReplicaSet הוא הבעלים של ה-Pod
kubectl get pods -o yaml | grep -A 5 ownerReferences

# אימות שה-Deployment הוא הבעלים של ה-ReplicaSet
kubectl get replicaset <replicaset-name> -o yaml | grep -A 5 ownerReferences

# --- 3. בדיקת יכולת "ריפוי עצמי" (Self-Healing) ---

# מחיקת Pod ספציפי (כדי לראות איך ה-ReplicaSet מקים אחד חדש מיד)
kubectl delete pod <pod-name>

# מעקב בזמן אמת אחר השינויים ב-Pod-ים (מחיקה ויצירה מחדש)
kubectl get pods -w

# בדיקת ה-"generation" של ה-Deployment (מוכיח שהבקר לא הושפע ממחיקת Pod)
kubectl get deployment nginx-deployment -o yaml | grep -A 2 "generation:"

# --- 4. ניהול עדכונים (Rolling Updates) ---

# עדכון גרסת ה-Image של הקונטיינר ב-Deployment
kubectl set image deployment/nginx-deployment nginx=nginx:1.22

# עריכה ידנית של ה-Deployment לעדכון גרסה או הגדרות
kubectl edit deployment nginx-deployment

# מעקב בזמן אמת אחר ה-ReplicaSets במהלך עדכון גרסה
kubectl get replicasets -w

# --- 5. ניהול גרסאות ושחזור (Rollback) ---

# צפייה בהיסטוריית הגרסאות (Revisions) של ה-Deployment
kubectl rollout history deployment/nginx-deployment

# הוספת הערה (Change Cause) לגרסה הנוכחית לצורך תיעוד
kubectl annotate deployment/nginx-deployment kubernetes.io/change-cause="Updated to nginx:1.22"

# צפייה בפרטי תבנית ה-Pod של גרסה ספציפית מהעבר
kubectl rollout history deployment/nginx-deployment --revision=1

# ביטול העדכון וחזרה לגרסה הקודמת (Undo)
kubectl rollout undo deployment/nginx-deployment

# חזרה לגרסה ספציפית מתוך ההיסטוריה
kubectl rollout undo deployment/nginx-deployment --to-revision=1

# --- 6. פתרון בעיות (Debugging) ---

# בדיקת סטטוס התקדמות של תהליך פריסה (Rollout)
kubectl rollout status deployment/nginx-deployment

# קבלת מידע מפורט על Pod (לצפייה באירועים ושגיאות)
kubectl describe pod <pod-name>

# צפייה בלוגים (Logs) של הקונטיינר בתוך ה-Pod
kubectl logs <pod-name>

# קבלת מידע מפורט על ה-Deployment (בדיקת תנאי כשל וזמני תגובה)
kubectl describe deployment nginx-deployment

# קבלת מידע מפורט על ה-ReplicaSet
kubectl describe replicaset <replicaset-name>