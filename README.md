//// Ця програма є системою для голосування за пропозиції. Вона дозволяє користувачам створювати нові пропозиції видавати квитки для голосування,здійснювати голосування "за" або "проти" пропозицій, а також переглядати результати голосування.Програма:



{

    struct ProposalInfo {
        title as field;
        content as field;
        proposer as address;
    }


    record Proposal {
        owner as address.private;
        id as field.private;
        info as ProposalInfo.private;
    }

    record Ticket {
        owner as address.private;
        pid as field.private;
    }

    mapping proposals {
        key as field.public;
        value as ProposalInfo.public;
    }

    mapping tickets {
        key as field.public;
        value as u64.public;
    }

    mapping agree_votes {
        key as field.public;
        value as u64.public;
    }

    mapping disagree_votes {
        key as field.public;
        value as u64.public;
    }

    // Функція для створення нової пропозиції
    function propose {
        input r0 as ProposalInfo.public;
        assert.eq self.caller r0.proposer;
        hash.bhp256 r0.title into r1 as field;
        cast self.caller r1 r0 into r2 as Proposal.record;
        async propose r1 into r3;
        output r2 as Proposal.record;
        output r3 as voting_system_kx0d8ki.aleo/propose.future;
    }

    finalize propose {
        input r0 as field.public;
        set 0u64 into tickets[r0];
    }

    // Функція для отримання нового квитка
    function new_ticket {
        input r0 as field.public;
        input r1 as address.public;
        cast r1 r0 into r2 as Ticket.record;
        async new_ticket r0 into r3;
        output r2 as Ticket.record;
        output r3 as voting_system_kx0d8ki.aleo/new_ticket.future;
    }



    finalize new_ticket {
        input r0 as field.public;
        get.or_use tickets[r0] 0u64 into r1;
        add r1 1u64 into r2;
        set r2 into tickets[r0];
    }

    // Функція для голосування "за"
    function agree {
        input r0 as Ticket.record;
        async agree r0.pid into r1;
        output r1 as voting_system_kx0d8ki.aleo/agree.future;
    }

    finalize agree {
        input r0 as field.public;
        get.or_use agree_votes[r0] 0u64 into r1;
        add r1 1u64 into r2;
        set r2 into agree_votes[r0];
    }



    // Функція для голосування "проти"
    function disagree {
        input r0 as Ticket.record;
        async disagree r0.pid into r1;
        output r1 as voting_system_kx0d8ki.aleo/disagree.future;
    }

    finalize disagree {
        input r0 as field.public;
        get.or_use disagree_votes[r0] 0u64 into r1;
        add r1 1u64 into r2;
        set r2 into disagree_votes[r0];
    }

    // Нова функція для перевірки результатів голосування
    function check_results {
        input r0 as field.public;
        get.or_use agree_votes[r0] 0u64 into r1;
        get.or_use disagree_votes[r0] 0u64 into r2;
        output r1 as u64.public;
        output r2 as u64.public;
    }

    // Нова функція для перегляду інформації про пропозицію
    function get_proposal_info {
        input r0 as field.public;
        get proposals[r0] into r1;
        output r1 as ProposalInfo.public;
    }

}


// тут кінець
