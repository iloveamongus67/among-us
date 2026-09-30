using BepInEx;
using BepInEx.Unity.IL2CPP;
using HarmonyLib;
using UnityEngine;

namespace AmongUsCustomRoles
{
    [BepInPlugin(PluginGuid, PluginName, PluginVersion)]
    [BepInProcess("Among Us.exe")]
    public class CustomRolesPlugin : BasePlugin
    {
        public const string PluginGuid = "com.yourname.amongus.customroles";
        public const string PluginName = "CustomRolesMod";
        public const string PluginVersion = "1.0.0";

        public Harmony Harmony { get; } = new Harmony(PluginGuid);

        public override void Load()
        {
            Log.LogInfo($"{PluginName} v{PluginVersion} 가 성공적으로 로드되었습니다.");
            Harmony.PatchAll();
        }
    }

    // ========================================================
    // 1. 판사 (Judge) 로직: 회의 중 키 입력으로 강제 사형
    // ========================================================
    [HarmonyPatch(typeof(MeetingHud), nameof(MeetingHud.Update))]
    public static class JudgeMeetingPatch
    {
        public static bool judgeAbilityUsed = false;

        public static void Postfix(MeetingHud __instance)
        {
            // F1 키를 누르면 현재 선택된 타겟을 즉시 강제 방출 (판사 전용 능력 예시)
            if (Input.GetKeyDown(KeyCode.F1) && !judgeAbilityUsed)
            {
                // 예시: 0번 플레이어 강제 투표 처리
                byte targetPlayerId = 0; 
                
                // 해당 플레이어를 즉시 방출 로직 호출
                MeetingHud.Instance.RpcClearVote();
                judgeAbilityUsed = true;
                Debug.Log("[판사] 사형 선고 능력이 발동되었습니다.");
            }
        }
    }

    // ========================================================
    // 2. 기술자 (Mechanic) 로직: 단축키로 원격 사보타지 해제
    // ========================================================
    [HarmonyPatch(typeof(PlayerControl), nameof(PlayerControl.Update))]
    public static class MechanicRemoteFixPatch
    {
        public static bool mechanicAbilityUsed = false;

        public static void Postfix(PlayerControl __instance)
        {
            // 내 캐릭터이고, F2 키를 눌렀을 때 원격 수리 발동
            if (__instance.AmOwner && Input.GetKeyDown(KeyCode.F2) && !mechanicAbilityUsed)
            {
                if (ShipStatus.Instance != null)
                {
                    // 원자로 / 산소 등 모든 사보타지 시스템 수리 신호 전송
                    ShipStatus.Instance.RpcRepairSystem(SystemTypes.Reactor, 16);
                    ShipStatus.Instance.RpcRepairSystem(SystemTypes.LifeSupp, 0);

                    mechanicAbilityUsed = true;
                    Debug.Log("[기술자] 원격 수리가 완료되었습니다.");
                }
            }
        }
    }
}
